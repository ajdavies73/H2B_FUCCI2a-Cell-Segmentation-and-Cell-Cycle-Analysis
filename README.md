# H2B_FUCCI2a Cell Segmentation and Cell Cycle Analysis

This repository contains two example workflows for segmentation of nuclei in epifluorescence images of live H2B_FUCCI2a_MCF7 cells, followed by per-nucleus and background measurements of fluorescence intensity in the RFP and YFP channels of the image. These values can be used to assess the relative levels of mCherry-hCdt1 and mVenus-hGeminin in each nucleus, from which the cell cycle position of individual cells in an image can be determined. This data is then used to assess changes in overall cell cycle distribution of a population of cells relative to control based on the relative proportions of 'red' (G1 phase), 'green' (S/G2/M phase), 'yellow' (early S phase) and 'no-colour' nuclei (G0).

This repository showcases the basis of these two workflows, an automated method using a purpose-built CellProfiler pipeline and a manual method for ImageJ/FIJI. Step-by-step instructions are provided for each method. ALl programs/scripts are available to download from the appropriate folders in the repository, and example datasets and analyses are also provided in these folders.

These workflows were developed in the Lindon group at the University of Cambridge (https://www.phar.cam.ac.uk/research/lindon) to automate segmentation of nuclei and measurement of per-nucleus mCherry-hCdt1 and mVenus-hGeminin fluorescence across a large number of live-cell epifluorescence images. These images were obtained from an Olympus IX81 microscope with a 40X NA 1.3 oil immersion objective. However, the workflows presented could also be applied to other FUCCI2a imaging data.

### What's in this repository?
**Section 1A** - Files needed for CellProfiler method to automate segmentation of nuclei and measurement of per-nucleus mCherry-hCdt1 and mVenus-hGeminin fluorescence across a large number of live-cell epifluorescence images simultaneously
  - CellProfiler pipeline
  - R script for merging images (.TIFF), often saved in individual folders following microscope image acquisition, into a single folder containing all .TIFF files for one experiment. This allows batch uploading of image files into CellProfiler for simultaneous analysis of all images obtained for a single condition or for all conditions in an experiment.
  - Example Excel spreadsheet for organising intensity results generated by CellProfiler for a whole experiment, with individual sheets within for each condition. The spreadsheet also contains a sheet per condition for per-image background intensity measurements, and allows automatic calculation of background-corrected mean intensity values using a simple formula in Excel.
  - Example CSV file into which per-nucleus background-corrected mean intensity values calculated above are copied for each single condition in an experiment. This file is used downstream to classify nucleus colours in the image and perform cell cycle phase distribution analyses.
  
**Section 1B** - Word document describing the protocol for manual segmentation of nuclei and measurement of per-nucleus mCherry-hCdt1 and mVenus-hGeminin fluorescence in ImageJ

**Section 2** - Example downstream analyses that can be performed on data obtained from CellProfiler or ImageJ image analysis in order to assess cell cycle phase distribution of a sample of cells in each image
  - R script for setting thresholds for background-corrected mCherry-hCdt1 and mVenus-hGeminin fluorescence levels in order to classify individual nuclei as 'red' (G1 phase), 'green' (S/G2/M phase), 'yellow' (early S phase) and 'no-colour' nuclei (G0). This script also calculates the proportion of nuclei in the total sample of each colour and therefore in each phase of the cell cycle, and plots as a stacked bar graph.
  - Example Excel spreadsheet containing data obtained from either of the two methods above, used to create graphs of per-nucleus values of mVenus-hGeminin intensity against mCherry-hCdt1 intensity, producing a graphical representation of distribution of all cells from a single condition across the cell cycle based on their relative positions on the two axes.
  - Word document describing additional methods of cell cycle analysis based on the data above with examples and protocols. These include analysing changes in mean mCherry/mVenus fluorescence intensity ratios for each population, and frequency distributions of single-cell mCherry/mVenus ratios to visualise changes in the distribution of the population of cells relative to control based on the gradient of the frequency distribution.

## Introduction to the H2B_FUCCI2a reporter system in H2B_FUCCI2a_MCF7 cells

H2B_FUCCI2a_MCF7 is a human breast adenocarcinoma cell line stably expressing the tricistronic Fluorescent Ubiquitination-based Cell-cycle Indicator (FUCCI) 2a reporter system, as shown in Fig. 1 below. This was generated using the Invitrogen™ FlpIn™ system (Matthew Ford, University of Edinburgh, 2017, https://era.ed.ac.uk/handle/1842/23658; Mort et al., 2014, https://doi.org/10.4161/15384101.2015.945381). This reporter system consists of the coding sequences of mCerulean fused to histone H2B (mCerulean-H2B), mCherry fused to amino acid residues 30-120 of hCdt1 (mCherry-hCdt1(30/120), henceforth referred to as mCherry-hCdt1) and mVenus fused to amino acid residues 1-110 of hGeminin (mVenus-hGeminin(1/110), henceforth referred to as mVenus-hGeminin). These coding sequences are fused by those of porcine Teschovirus-1 2a (P2a) and Thosea asigna virus 2a (T2a) self-cleaving peptides, which separate the three fusion proteins following translation. Expression of this construct is under the control of a CAG constitutive promoter, and cells express equimolar quantities of mCerulean-H2B, mCherry-hCdt1 and mVenus-hGeminin.

Endogenous Cdt1, a nuclear protein, increases in abundance from late mitosis through G1 phase to early S phase, then is targeted for degradation by SCF(Skp2) E3 ubiquitin ligase at the G1/S phase transition. Undegraded Cdt1 is inhibited by geminin, a nuclear protein, whose abundance increases from S phase through G2 phase to early mitosis before being targeted for degradation by APC/C(Cdh1) E3 ligase. These E3 ligases in turn control the activity of the other, with Skp2 targeted for degradation in early G1 phase by APC/C(Cdh1) (Bashir et al., 2004, https://doi.org/10.1038/nature02330), leading to peak activity in S/G2. Active SCF in complex with β-TRCP targets Cdh1 for degradation at the G1/S transition (Fukushima et al., 2013, https://doi.org/10.1016/j.celrep.2013.07.031), leading to peak activity in late M/G1. 

This results in an accumulation of the mCherry-hCdt1 probe in G1 and accumulation of the mVenus-hGeminin probe in S/G2/M phases. H2B_FUCCI2a_MCF7 cells therefore fluoresce red in G1, yellow in early S (where both mCherry-hCdt1 and mVenus-hGeminin are detectable) and green in late S/G2/M phases. By analysing the relative fluorescence of mCherry-hCdt1 and mVenus-hGeminin, and thus their relative abundances, it is possible to estimate the cell cycle phase of each individual cell. This can be used to assess changes in overall cell cycle distribution of a population of cells relative to control.

<br>
<img src="https://github.com/user-attachments/assets/53db4b53-6f66-4c51-bb09-ac9e11d1d791" width="600">
<img src="https://github.com/user-attachments/assets/823e9d71-392a-4904-9fb9-36390cbf7aad" width="600">
<br>

_**Fig. 1: H2B_FUCCI2a_MCF7 cells expressing the H2B_FUCCI2a tricistronic cell cycle reporter system.** **(A)** Schematic depiction of the H2B_FUCCI2a tricistronic construct stably expressed by the H2B_FUCCI2a_MCF7 cell line used in this experiment. Figure created with BioRender.com. **(B)** Schematic representation of the overall ‘colour’ of the H2B_FUCCI2a_MCF7 cell nuclei throughout the cell cycle based on the relative levels of mCherry-hCdt1 and mVenus-hGeminin. Figure created with BioRender.com. **(C)** Representative 40X epifluorescence image of a sample of GL2i-treated cells, showing the differential interference contrast (DIC) image overlaid with the epifluorescence images of mCerulean-H2B, mCherry-hCdt1 and mVenus-hGeminin. **(D)** Merge of the DIC image and the epifluorescence images in each of the three channels. **(E)** Merge of the DIC image and the epifluorescence images in the RFP (mCherry-hCdt1) and YFP (mVenus-hGeminin) channels, showing the overall ‘colour’ of the nuclei in the sample based on the relative levels of mCherry-hCdt1 and mVenus-hGeminin in each cell. Arrow 1 demonstrates a ‘green’ nucleus in S/G2/M phase in which the fluorescent signal and thus level of mVenus-hGeminin exceeds that of mCherry-hCdt1; arrow 2 demonstrates a ‘red’ nucleus in G1 phase in which the fluorescent signal and thus level of mCherry-hCdt1 exceeds that of mVenus-hGeminin; arrow 3 demonstrates a ‘yellow’ nucleus in early S phase in which the fluorescent signals of mVenus-hGeminin and mCherry-hCdt1 are approximately equal. **(F)** Merge of the epifluorescence images in the RFP (mCherry-hCdt1) and YFP (mVenus-hGeminin) channels. All scale bars represent 50 μm._

# Section 1 - Methods to obtain per-nucleus and background fluorescence intensities in each channel

1. CellProfiler pipeline - this pipeline allows automatic segmentation of nuclei in a large number of images simultaneously. The per-nucleus intensity of fluorescence in each channel is measured automatically for each image and outputted as .csv files for downstream manipulation and data analysis. This is a high-throughput and less time-consuming method but generates a large number of unnecessary measurements which can make it difficult to find relevant data. This method is also more prone to errors in nucleus segmentation than manual detection in ImageJ as there is no option to review/refine automatically detected nucleus objects.
2. ImageJ segmentation method - a protocol for automatic detection of nuclei using thresholding to segment nuclei, with manual refinement of detected nuclei. This reduces the chance of errors in nucleus detection, but is more time-consuming than the CellProfiler method and can only be used to analyse one image at a time.

## Section 1A - CellProfiler Method

### CellProfiler pipeline

This CellProfiler pipeline was developed by Amelie Davies, February 2024, for automatic segmentation of nuclei of H2B_FUCCI2a_MCF7 cells in a large number of epifluorescence images simultaneously. The pipeline then measures per-nucleus fluorescence intensity in each channel automatically, as well as in a manually identified background region. The data is outputted as .csv files, and can be used to assess the position of each cell in the cell cycle based on the relative background-corrected fluorescent intensities in the RFP and YFP channels, corresponding to mCherry-hCdt1 and mVenus-hGeminin levels respectively.

Protocol for using the CellProfiler pipeline:
1. Download CellProfiler from https://cellprofiler.org/. CellProfiler is a free, open-source software program designed to enable biologists without training in computer vision or programming to quantitatively measure cell phenotypes from thousands of images automatically.
2. Download the file named CellProfiler FUCCI Cell Cycle Analysis Pipeline (H2B_FUCCI2a_MCF7) Amelie Davies 20241115.cpproj from Section 1A folder in this repository, and open in CellProfiler. This is the custom-designed CellProfiler pipeline for analysing images of H2B_FUCCI2a cells.
3. If images from microscopy have been saved into separate folders for each image (e.g. when using MicroManager for image acquisition) and are named according to the image number/condition, use the R script in the Section 1A folder of this repository to merge all .TIFF image files into a single folder for a whole experiment.
4. Load all images for a single condition into the 'Images' module of the CellProfiler pipeline.
5. Click 'Extract metadata' in the Metadata tab to extract information describing your images from the image file headers, which will be stored along with your measurements.
6. The NamesAndTypes module allows you to assign a meaningful name to the channels of image by which other modules will refer to it. Use the metadata and Names and Types module to identify channels. E.g.:
- CFP channel: used to image mCerulean-H2B fluorescence, allowing identification of entire nucleus throughout the cell cycle. This facilitates single-cell tracking.
- YFP channel: used to image mVenus-hGemini fluorescence, accumulating in S/G2/M phase nuclei and allowing identification of cells in S/G2/M.
- RFP channel: used to image mCherry-hCdt1 fluorescence, accumulating in G1 phase nuclei and allowing identification of cells in G1.
- DIC channel: differential interference contrast (DIC) images of cells, enhancing contrast of brightfield images for visualisation of transparent whole cells.
7. This tab is currently configured to automatically assign frame 0 of each image as CFP, frame 1 as YFP, frame 2 as RFP and frame 3 as DIC. Before continuing, ensure that the frame numbers associated with each channel match those associated with your image files! This may also need to be changed to C matching or ChannelName matching depending on your image files.
8. Click 'Update' at the bottom of the NamesAndTypes page to automatically split each image into its constituent channels/frames as described above. Each individual channel is shown in its own labelled column.
9. The IdentifyPrimaryObjects tab automatically identifies nuclei based on mCerulean-H2B fluorescence in the CFP channel of each image. Adapt thresholding based on your images to optimise nucleus identification. Identified nuclei are declumped based on object shape where they are clustered in an image and cannot initially be identified as separate objects. In order to optimise thresholding, the following parameters can be adjusted:
- Nucleus diameter: 35-120 pixel units - adjust based on observed diameter of nuclei in your images. 
- Threshold smoothing scale - smoothing improves the uniformity of the identified objects, and this value may need to be adjusted to improve identification of nuclei. Increasing this value will increase smoothing of CFP staining before thresholding to make identified nuclei more uniform. Decreasing this value will reduce smoothing before thresholding, which may allow more accurate capturing of the detail in nucleus shape in the identified nuclei. The scale should be approximately the size of the artifacts to be eliminated by smoothing. A Gaussian is used with a sigma adjusted so that 1/2 of the Gaussian's distribution falls within the diameter given by the scale (sigma = scale/0.674). 
- Threshold correction factor - this can be increased to make nucleus identification more stringent, and decrease to make nucleus identification more lenient.
10. 

### Example Excel spreadsheet for pooling results from different conditions and background subtraction

### Example CSV file for single condition background subtracted per-nucleus fluorescence intensities for thresholding

### R script to merge images into a single folder

This script can be used to merge several TIFF files into a single folder containing all image files, where each TIFF file is stored by the microscope/MicroManager in separate folders. This allows batch uploading of image files into the CellProfiler pipeline (e.g. to allow automatic analysis of all images for a single condition simultaneously).

N.B. the source directory containing image folders and the desired target directory for all image files will need to be manually specified according to your computer and file structure.

## Section 1B - ImageJ/FIJI Method

# Section 2 - Analysing per-nucleus intensity data to assess cell cycle phases and progression

## FUCCI thresholding code

This script can be used to classify nuclei from a single condition as red (corresponding to G1 phase), green (S/G2/M phase), yellow (S phase) or no-colour (G0). The proportions of nuclei of each colour in the sample are calculated and plotted as a stacked bar chart.

N.B. your .csv file for the condition to be analysed must be formatted as two columns with the headers 'RFPminusBG' and 'YFPminusBG'. These should contain the per-nucleus mean intensities of RFP and YFP fluorescence with the background RFP and YFP fluorescence intensity for the image subtracted respectively. Each row in the file should represent a single nucleus.

You may need to install the package 'tidyverse' before running this script; this can be done using the command install.packages("tidyverse") in the console.

Graphical representation:
<br>
<br>
<img src="https://github.com/user-attachments/assets/25adf5d9-72d8-42d7-9618-e6a83d7bb006" width="700">
<br>
_**Fig. 2: Schematic representation of the method used to determine the overall 'colour' of the nucleus of a single H2B_FUCCI2a_MCF7 cell.** Orange dashed lines indicate the mean mCherry or mVenus fluorescence intensity values across the population of control cells (GL2i-transfected) for each biological replicate of the experiment. Nuclei were classified as ‘red’ (corresponding to G1 phase cells) if their mean mCherry-hCdt1 fluorescent signal was greater than the median value for the control population and their mean mVenus-hGeminin fluorescent signal was less than the median for the control population. Nuclei were classified as ‘green’ (corresponding to late S/G2/M phase cells) if their mean mVenus-hGeminin fluorescent signal was greater than the median value for the control population and their mean mCherry-hCdt1 fluorescent signal was less than the median value for the control population. Nuclei were classified as yellow (corresponding to early S phase cells) if both their mean mCherry-hCdt1 and mVenus-hGeminin signals were greater than the median values for the control population. Nuclei were classified as ‘no colour’ where both their mean mCherry-hCdt1 and mVenus-hGeminin signals were less than the median values for the control population._

## Other analysis

Excel - plotting RFP against YFP values for each nucleus, using distribution to estimate cell cycle position of cells
Komolgorov-Smirnov line plots of RFP/YFP ratios against frequency
Plot log(mVenus/mCherry) ratio relative to GL2i

Look at stats for each and example data/figures

## Outro 

Written by Amelie Davies, March 2024 and updated November 2024.
<br>
All files, images and data belong to Lindon Group, Department of Pharmacology, University of Cambridge, UK.
<br>
<br>
&copy; Copyright 2024, Amelie Davies.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0. Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
