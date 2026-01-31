# Automobile Testing V-Model Documentation

This project contains comprehensive documentation for automobile testing using the V-Model methodology.

## Overview

The V-Model is a software development and testing methodology that emphasizes verification and validation at each stage of the development lifecycle. This documentation applies the V-Model specifically to automobile testing.

## Documentation Structure

- **Requirements Analysis**: System and component requirements
- **Design Specifications**: High-level and detailed design
- **Testing Phases**: Unit, integration, system, and acceptance testing
- **V-Model Implementation**: Complete lifecycle documentation

## Building Documentation

### Prerequisites
```bash
pip install sphinx sphinx-rtd-theme
```

### Build HTML Documentation
```bash
cd docs
make html
```

The generated documentation will be available in `docs/_build/html/index.html`

## Project Structure
```
DaC/
├── docs/
│   ├── source/
│   │   ├── index.rst
│   │   ├── conf.py
│   │   ├── v-model/
│   │   ├── requirements/
│   │   └── testing/
│   └── Makefile
├── README.md
└── .github/
    └── workflows/
        └── docs.yml
```

## GitHub Pages

Documentation is automatically built and deployed to GitHub Pages on push to main branch.

## License

MIT License
