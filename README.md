<p align="center">
  <img src="assets/ic50.png" alt="logo" width="40" height="40"/>
  <h1>Automated dose-response curve analysis using 4-parameter logistic regression.</h1>
</p>

To run, ensure that your spreadsheets containing relevant tables are included
within the 'datasets' folder. The name of the file does not matter, nor do the number of
pages included.

Further, ensure that if you wish to use SAR mappings, include the SAR mapping
table titled 'SAR Compound List.xlsx' in the datasets folder. This table should
have two columns labeled 'Compound' and 'Parent'. If no file is included,
the program will run without mappings.

Run the executable, whilch will prompt you to accept default values or adjust.

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
