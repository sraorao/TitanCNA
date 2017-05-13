# file:   README - Notes on TitanCNA version updates
# author: Gavin Ha 
# 		  Dana-Farber Cancer Institute
#		 	  Broad Institute
# contact: <gavinha@gmail.com> or <gavinha@broadinstitute.org>
#
# TitanCNA GitHub: https://github.com/gavinha/TitanCNA
# TitanCNA utils: https://github.com/gavinha/TitanCNA-utils
# Google Groups: https://groups.google.com/forum/#!forum/titancna 
# TitanCNA website: http://compbio.bccrc.ca/software/titan/
#
# date:	  May 10, 2017
#
# description: TitanCNA is a probabilistic framework for analyzing subclonal copy number alterations (CNA) and loss of heterozygosity (LOH) in whole genome sequencing of tumours. The main software is implemented as a R Bioconductor package with preprocessing tools implemented in Python and BAMtools (C++). The example provided will reproduce Figure 1 in the manuscript. However, it will be slightly different because the example is only based the analysis of chr2, not genome-wide.

The license for the R Bioconductor package is now open source under GPLv3.  This applies to the v1.9.0 and all subsequent versions within and obtained from Bioconductor.  
Because the Bioconductor SVN is bridged to the GitHub TitanCNA repository "master" branch, the GitHub master branch for TitanCNA will also be under GPLv3.  
Please note that any other branch in the GitHub TitanCNA repository is still under the previous academic license. Users who are using TitanCNA for the purpose of academic research are free to use all branches and versions of the software. If this does not apply to you, please contact gavinha@broadinstitute.org, sshah@bccrc.ca, and prebstein@bccancer.bc.ca.

Thank you for your interest in the TitanCNA software.

=================================================================================
TitanCNA version 1.15.0 changes 
=================================================================================
1) 10X Genomics analysis
  - Please see https://github.com/gavinha/TitanCNA-utils for instructions on running the 10X Genomics data

2) New function
  - plotSegmentMedians()
  - loadHaplotypeAlleleCounts(): loads input allele counts with phasing information
  - plotHaplotypeFraction(): results from 10X Genomics WGS data with phasing of haplotype blocks
  

3) Modified features (no changes for user-accessible functions)
  - updateParameters: coordinate descent estimate of ploidy update uses previously estimated normal parameter from the same corodinate descent iteration ; leads to faster convergence
  - fwd_back_clonalCN.c: returns 2-slice marginals 

=================================================================================
TitanCNA version 1.10.1 changes 
Changed to v1.12.1 to match Bioconductor versioning
=================================================================================
1) New function
	- outputTitanSegments(): Replaces the need for external Perl script to generate segments file.
	
2) Modified function
	- setupClonalParameters(): Initializes cellular prevalence values evenly distributed between 0 and 1 rather than 0.1 increments.
	
	- loadDefaultParameters(): added hetBaselineSkew argument which will determine the baseline allelic fraction to use.  If this is NULL, then will set using the median allelic ratio across the genome ("data" argument).  If "data" argument is NULL, then will simply set to 0.55 (heterozgyous skew of 0.05). Note that the "skew" argument applies to all other states except for the heterozygous allelic states (e.g. 1:1, 2:2, 3:3, 4:4 - for minor:major copies).

3) Bug fixes
	- plotClonalFrequency(): Handle case when result is completely copy neutral heterozygous and cellular prevalence and ClonalClusters columns are both NA
	
	- runEMclonalCN(): If EM ever decreases, then take last iteration (with higher likelihood).
	
	- filterData(): Remove centromere positions now working. 

=================================================================================
TitanCNA version 1.7.2 changes (Committed revisions 6796790)
Merged to master as v1.9.0 to match Bioconductor versioning
=================================================================================
1) New function to remove clonal clusters with few or no data
	- removeEmptyClusters(): This function can help prune down the number of clonal clusters for which there are few alterations. 
2) Updated functions
	- plotCNlogRByChr(): added segment plotting when given list of segments 
	- plotCNlogRByChr(): accepts normal contamination estimate so that the average tumor ploidy adjustment is correctly tumor-specific.  
3) Updated dependencies
	- R (>= 3.2.0), foreach (>= 1.4.2), IRanges (>= 2.2.4), Rsamtools (>= 1.20.4), GenomeInfoDb (>= 1.4.0)
4) New dependencies
	- GenomicRanges (>= 1.20.4)
5) Bioconductor package license changed to GPLv3.

=================================================================================
TitanCNA version 1.5.8 changes (Committed revisions 977c48a,5fe5acc.)
Merged to master as v1.7.1 (a5bf4f3)
=================================================================================
1) Updated functions to align with dependent package updates.
	- Affected function: loadAlleleCounts, correctReadDepth
	- Adjusted function to accommodate GenomeInfoDb v1.1.3 -> v1.2.4
	- Usage of this function remains unchanged.

2) Minor bug fix in function
	- Affected function: correctReadDepth
	- Issue with findOverlaps() usage fixed

3) Bug fix
	- Affected function: outputTitanResults, plotClonalFrequency, plotSubcloneProfiles
	- Fixed bug when error is thrown trying to output results for genome that is predicted to be completely diploid HET.

4) Modified internal functionality
	- Affected function: runEMclonalCN, viterbiClonalCN
	- The C code (src/fwd_backC_clonalCN.c & src/viterbiC_clonalCN.c) have been modified such that the transition probabilities between clonal clusters for HET (diploid) states are equal.  This may help prevent situations when normal contamination is 0 for more than 1 clonal cluster solutions.

=================================================================================
TitanCNA version 1.5.7 changes (Committed revision 98539.)
=================================================================================
1) Fixed bug in utils.R, src/fwd_backC_clonalCN.c, and src/viterbiC_clonalCN.c
	- unfinished changes were accidentally committed in v1.5.6.
	- rolled "loadDefaultParameters" back to v1.5.5

=================================================================================
TitanCNA version 1.5.6 changes (Committed revision 98115.)
=================================================================================
1) Fixed bug in getPositionOverlap
	- Affected function: getPositionOverlap
	- This function should return log ratio values based on overlap of SNPs ("chr" & "posn") with the input list of regions "dataVal". The bug causes the return of values that are incorrectly matched to the input coordinates. This bug is more prominent when using targeted sequencing (i.e. exome) data. In some cases, this can lead to missing data and/or chromosomes in the final result. This has been fixed.

2) Added option to remove SNPs near centromeres
	- Affected function: filterData
	- This function can now remove SNPs at and/or near centromeres. The "centromeres" argument (default NULL) accepts a data.frame with 3 columns: chromosome, start, stop.  Argument "centromere.flankLength" (default 0) accepts an integer specifying the length of region to the left and right of the centromere to also exclude overlapping SNPs.
	
3) Updated vignette to include latest changes from v1.5.5. The modification of loadDefaultParameters to include the loaded data from loadAllelicCounts to inform the baseline allelic ratio parameters.

4) Modified documentation manuals
	- Affected man page: loadDefaultParameters
	- Fixed language and grammar, and improved clarity

=================================================================================
TitanCNA version 1.5.5 changes (Committed revision 96727.)
=================================================================================
1) Modified function
	- Affected function: loadDefaultParameters
	- Added argument: data
	- For symmetric=TRUE, the heterozygous states (4,9,25) are set to the median allelic ratio of the entire genome if data is given
	- data is the output from loadAlleleCounts()
	- This corrects the issue where the entire genome is mostly copy neutral LOH (NLOH) when it should most likely be copy neutral heterozygous (HET).
	
2) Modified function
	- Affected function: outputModelParameters
	- Argument removed: S_Dbw.data.type
	- Will output S_Dbw for datatypes: LogRatio, AllelicRatio, and Both
	- "Both" refers to the sum of both datatypes (LogRatio and AllelicRatio) for each of the dens.bw and scat components 

=================================================================================
TitanCNA version 1.5.4 changes (Committed revision 96687.)
=================================================================================
1) Model selection default changed
	- Affected function: outputModelParameters
	- New value can be used for argument: S_Dbw.data.type = "Both"
	- "Both" will sum the S_Dbw index for "AllelicRatio" and "LogRatio"; this will account for both datatypes when selecting the best solution
	- outputModelParameters() will now return the S_Dbw information
	
2) Vignette additions
	- Under section "Format and print results to file"
		- added instructions for using S_Dbw scale to penalize higher number of clonal clusters
		
3) Updated references in manual pages
	- Ha et al. (2014). Genome Research, 24: 1881-1893.
	
=================================================================================
TitanCNA version 1.5.3 changes (Committed revision 96468.)
=================================================================================
Note: This is the development version
1) Minor implementation change
	- Affected function: viterbiClonalCN, runEMclonalCN
	- Probabilities for transitioning into different clonal clusters was originally txnZstrength*txnExpLen and specified in the C code.  It is now decoupled from the C code and specified in the R code, instead. 

2) Vignette correction/additions
	- Under section "Filter the data", changed to:
		- data <- filterData(data, c(1:22, "X", "Y"),...
	- Under section "Format and print results to file"
		- added instructions for using S_Dbw scale to penalize higher number of clonal clusters

3) Bug fixes
	- Affected function: plotSubcloneProfiles
	- Whole genome profile plots of subclones (using chr=NULL) was not working properly, previously.
	
=================================================================================
TitanCNA version 1.5.2 changes (Committed revision 95883.)
=================================================================================
Note: Bioconductor v3.1 (development)
1) Modified functions
	- Affected function: getPositionOverlap
	- Now uses RangedData objects and findOverlaps utility function internally
	- getPositionOverlap is now much faster than before 

=================================================================================
TitanCNA version 1.5.1 changes (Committed revision 95701.)
=================================================================================
Note: Bioconductor v3.1 (development)
1) Added parameter arguments
	- Affected function: loadAlleleCounts
		- argument "header" added so users can indicated if input counts file contains a header line
2) Error checking
	- Affected function: loadAlleleCounts
		- Added file format checking to ensure chromosome coordinates and read counts are integers. (note chromosomes are still characters to accommodate X and Y)
	
	
=================================================================================
TitanCNA version 1.3.0 changes (Committed revision 95439.)
=================================================================================
1) Changed default parameters
	- Affected function: loadDefaultParameters
		- genotypeParams$rt is changed from 0.08 to 0.05 logR noise scalar to heterozygous states
2) Added new functionality and options to computation of S_Dbw validity index
	- Affected public function: computeSDbwIndex 
	- Added argument "data.type"
		- data.type can be set to "LogRatio" (default) or "AllelicRatio"
		- For samples that have stronger alteration signals in allelic ratio data compared to log ratio data can use "AllelicRatio" to compute S_Dbw index for model selection 
	- Changed argument: 'method' to 'centroid.method'
	- Added functionality of choosing between "Halkidi" or "Tong" S_Dbw method
		- New argument 'S_Dbw.method' can be set to "Halkidi" or "Tong" (default)
		- References: 
			- Halkidi and Vazirgiannis (2001). Clustering Validity Assessment: Finding the Optimal Partition of a Data Set
			- Tong and Tan (2009) Cluster validity based on the improved S_Dbw index
	- Note that for Tong's method, the computation of SCAT(c) is likely incorrect.  This function uses ni/N instead of (N-ni)/N.
3) Added and updated arguments to outputModelParameters() function
	- Affected public function: outputModelParameters
		- Added arguments S_Dbw.data.type (default 'LogRato'), S_Dbw.scale (default 1), S_Dbw.method (default "Tong").

=================================================================================
TitanCNA version 1.2.1 changes (Committed revision 92909.)
=================================================================================
1) Critical bug fix for memory usage
	- Affected public functions: runEMclonalCN
	- bug in memory management in C implementation of logsumexp for forwards-backwards algorithm
2) Fixed bug for decoding TITAN state when symmetric = FALSE
	- Affected public functions: loadAlleleCounts, runEMclonalCN, outputTitanResults
	- loadDefaultParameters now returns list element "symmetric". This is then propagated to being a list element in the output of the runEMclonalCN function.  In turn, this output is required in the function outputTitanResults. outputTitanResults now requires "symmetric" element from convergeParams argument to determine whether the allelic ratio data was analyzed under symmetric mode.
3) Changed default parameters
	- Affected functions: loadDefaultParameters
	- genotypeParams$alphaKHyper initialized to 15,000 prior counts for all states
4) Updated Vignette with Subclone profile output
	- Affected function: outputTitanResults
	- Additional argument, "subcloneProfiles" to indicate whether to output subclone profiles. This only works for 1 or 2 clonal clusters in the solution
	- New function: plotSubcloneProfiles
	- Plots the copy number profile for the predicted subclones. Only works for 1 or 2 clonal clusters in the solution 


=================================================================================
TitanCNA version 1.2.0 changes (Committed revision 91120.)
=================================================================================
1) Added new functionality for extracting read counts from BAM files
	- New function: extractAlleleReadCounts
2) Added support for conversion between UCSC and NCBI chromosomes in input files
	- Affected functions: loadAlleleCounts, correctReadDepth
3) Changed default parameters
	- Affected functions: loadDefaultParameters
	- genotypeParams$rt now has a 0.08 logR noise scalar added to heterozygous states
	- genotypeParams$alphaKHyper initialized to 5,000 prior counts for all states; extreme states (HOMD and 4 copies or higher) are initialized to 15,000.
4) Forwards-Backwards Algorithm now computes the posterior in log space 
	- Affects: runEMclonalCN, fwd_backC_clonalCN.c 
	- Now enables users to use coarser segmentation settings (txnExpLen and txnZstrength arguments in runEMclonalCN)
5) Added new functionality for interpreting subclone profiles
	- Affects: outputTitanResults
	- For 2 clonal cluster solutions, will return 2 subclone profiles appended to results data.frame
	- New function: plotSubcloneProfiles
	- Plots the 2 subclone profiles	
6) Fixed bug for interpreting the final 24th state (when symmetric=TRUE)
	- Affected functions (private): decodeLOH

