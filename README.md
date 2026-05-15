# Automated dose-response curve analysis using 4-parameter logistic regression.

v1.0.0

To run, ensure that your spreadsheets containing relevant tables are included
within the 'datasets' folder. The name of the file does not matter, nor do the number of
pages included.

Further, ensure that if you wish to use SAR mappings, include the SAR mapping
table titled 'SAR Compound List.xlsx' in the datasets folder. This table should
have two columns labeled 'Compound' and 'Parent'. If no file is included,
the program will run without mappings.

Run IC50_Analysis.exe, which will prompt you to specify which files you want
to process.

This will create a folder called 'results', which, for each table, will
generate figures showing the fitted curves and a summary of the fits. Once all
tables have been run, a file called 'analysis_summary.xlsx' will be generated
which summarizes all compounds from all tables. Any rows highlighted in red
signify either 1: a valid IC50 value could not be found, or 2: the R^2 value
is below 0.90.

Note that while the top and bottom asymptotes are fit based on the data (per
compound), the lower bound is held constant at 0.045. Thus, if the top is fit
at 0.55 and the bottom is fit at 0.06, then IC50 is calculated where Y =
(0.55-0.045) rather than (0.55-0.06). This aligns with the absolute IC50
rather than the relative IC50.

Update 2026.01.16: Added MIC values and contamination alerts.
Update 2026.01.22: Added log2 and log10 options.
Update 2026.01.23: Added SAR mapping, updating both table summaries and full summary spreadsheet.
Update 2026.05.10: Response threshold for MIC determination changed from 0.085 changed to 0.089 and added adjustment prompt.

If you have any questions or comments, please email me (CASTR5@ulaval.ca
or cstrick4@uwo.ca).
