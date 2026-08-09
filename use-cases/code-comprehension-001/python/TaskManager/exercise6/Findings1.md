# DataInsight

A data analysis and visualization framework for scientific data.

## Installation

1. Install Python 3.8 or newer.
2. Create and activate a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate   # macOS / Linux
   venv\Scripts\activate      # Windows
   ```
3. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib plotly jupyter
   ```

## Basic Usage

### Import the package
```python
from datainsight import DataInsight
```

### Load data
```python
from datainsight.io import CsvLoader

data = CsvLoader.load("data/sample.csv")
```

### Clean and transform
```python
from datainsight.transform import Cleaner

cleaned = Cleaner.drop_missing(data)
transformed = Cleaner.normalize_columns(cleaned)
```

### Analyze
```python
from datainsight.analysis import Stats

summary = Stats.describe(transformed)
```

### Visualize
```python
from datainsight.viz import PlotlyChart

PlotlyChart.line(transformed, x="time", y="measurement")
```

### Generate a report
```python
from datainsight.report import ReportGenerator

report = ReportGenerator.create(summary, charts=[...])
report.save("output/report.html")
```

## Features

- Data import from CSV, Excel, SQL databases, and APIs
- Data cleaning and transformation tools
- Statistical analysis functions
- Interactive visualizations
- Report generation
- Export capabilities

## Configuration

Configuration can be provided through a settings object or file:

- `input_format`: CSV, Excel, SQL, API
- `output_format`: HTML, PDF, Excel
- `visualization_theme`: default, dark, light
- `report_template`: built-in templates for summary reports
- `database_connection`: connection string for SQL import/export

Example:
```python
from datainsight import Config

config = Config(
    output_format="html",
    visualization_theme="dark",
    report_template="scientific"
)
```

## Troubleshooting

- `ImportError`: verify dependencies are installed and virtual environment is active.
- `ValueError` on loading files: check that file path and format match the loader.
- `ConnectionError` for SQL/API imports: verify network access and credentials.
- Visualization issues: ensure `plotly` and `matplotlib` are installed and the notebook environment supports rendering.

## Contributing

1. Fork the repository.
2. Create a feature branch:
   ```bash
   git checkout -b feature/my-feature
   ```
3. Add tests under `/tests`.
4. Run tests:
   ```bash
   pytest
   ```
5. Submit a pull request with a clear description.

## License

This project is released under the MIT License.
