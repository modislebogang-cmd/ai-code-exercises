# DataInsight FAQ

## Getting Started

**Q: What is DataInsight?**
A: DataInsight is a scientific data analysis and visualization framework designed to import, clean, analyze, visualize, and export data from CSV, Excel, SQL, and APIs.

**Q: What do I need to install first?**
A: Install Python 3.8+ and the required packages: `pandas`, `numpy`, `matplotlib`, `plotly`, and `jupyter`.

**Q: How do I open the project?**
A: Open the repository in your code editor, activate a Python virtual environment, install dependencies, and start with the `/examples` notebooks or import modules from `datainsight`.

**Q: Where should I put my data files?**
A: Store sample input files in a local data directory or point import utilities to the correct CSV, Excel, or SQL source path.

## Common Features

**Q: How do I import data from CSV or Excel?**
A: Use the `datainsight.io` loaders. CSV uses `pandas.read_csv()` and Excel uses `pandas.read_excel()` under the hood.

**Q: Can DataInsight read from SQL or APIs?**
A: Yes. SQL import should use `sqlalchemy` plus `pandas.read_sql()`, and API import should use `requests` with JSON parsing.

**Q: How do I clean data?**
A: Use `datainsight.transform` utilities for normalization, missing-value handling, data type conversion, and filtering.

**Q: What analysis does DataInsight support?**
A: It supports summary statistics, grouped aggregations, correlation metrics, distribution analysis, and custom scientific metrics.

**Q: How can I create visualizations?**
A: Use `datainsight.viz` functions to generate Matplotlib static plots or interactive Plotly charts.

**Q: How do I generate reports?**
A: Use `datainsight.report` to create HTML or PDF reports that combine summaries, charts, and tables.

## Troubleshooting

**Q: My import fails with a file error. What should I check?**
A: Confirm the file path exists, the format matches the loader, and the file encoding is correct.

**Q: Why does my analysis output look wrong?**
A: Check that data cleaning was applied, numeric columns are not mixed with strings, and missing values were handled consistently.

**Q: Plots are not rendering in my notebook. What now?**
A: Ensure `plotly` and `matplotlib` are installed and the notebook renderer is enabled. For Jupyter, use the correct Plotly renderer or `%matplotlib inline`.

**Q: Report generation fails. What is the likely cause?**
A: Verify that chart objects are valid, the data passed to the report builder is non-empty, and report templates are configured correctly.

**Q: I am seeing too many errors to debug. How should I approach this?**
A: Start by isolating the failing component: import, transform, analysis, or visualization. Add small unit tests in `/tests`, validate each pipeline step, and remove one source of complexity at a time.

## Specific Area of Interest: Data Import

**Q: What should I do if SQL import returns no rows?**
A: Check the SQL query, verify database credentials, and confirm the target table contains data.

**Q: How do I handle API rate limits?**
A: Implement retries with backoff and cache responses locally where possible.

**Q: What is the best way to normalize column names?**
A: Use a transformation utility to lowercase names, replace spaces with `_`, and standardize field labels before analysis.

## Additional Notes

- DataInsight is optimized for scientific workflows, so consistent formatting and data validation are essential.
- If common errors persist, use the `/tests` directory to capture and reproduce failing conditions before applying fixes.
