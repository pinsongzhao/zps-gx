# GalfitX Documentation Website Guide

## Table of Contents
1. [Project Overview](#project-overview)
2. [Features and Capabilities](#features-and-capabilities)
3. [Installation and Setup](#installation-and-setup)
4. [Project Structure](#project-structure)
5. [Core Components](#core-components)
6. [Usage Examples](#usage-examples)
7. [Configuration Files](#configuration-files)
8. [Building Documentation Site](#building-documentation-site)
9. [Deployment](#deployment)

## Project Overview

GalfitX is a sophisticated astronomical image processing pipeline that provides automated source detection, image decomposition, and photometry measurements across multiple wavelengths. The project integrates the powerful GalfitS modeling software with custom preprocessing and postprocessing utilities to facilitate comprehensive galaxy analysis.

### Key Characteristics
- **Multi-wavelength Analysis**: Supports processing of multi-band imaging data
- **Automated Pipeline**: Implements fully automated source detection and fitting
- **Astronomical Focus**: Specifically designed for galaxy morphology analysis
- **Flexible Architecture**: Modular design with interchangeable components

## Features and Capabilities

### Core Functions
- **Source Detection**: Advanced detection algorithms using SExtractor-based methods
- **PSF Generation**: Point Spread Function creation and handling for multiple bands
- **Mask Creation**: Automated generation of masks for neighboring objects
- **Sky Estimation**: Accurate background subtraction algorithms
- **Model Fitting**: Integration with GalfitS for morphological parameter estimation
- **Photometry Measurements**: Precise flux and magnitude calculations

### Technical Features
- **Parallel Processing**: Support for multi-threaded execution
- **Segmentation Maps**: Generation of detailed segmentation maps
- **Postage Stamp Extraction**: Automated crop of individual sources
- **Multi-band Handling**: Consistent processing across different filters
- **Configurable Parameters**: TOML-based configuration system

## Installation and Setup

### Prerequisites
- Python 3.11 or higher
- GalfitS (external dependency)

### Dependencies
The project requires the following packages:
```txt
astropy==6.0.0
matplotlib==3.10.8
numba==0.60.0
numpy==1.26.4
pandas==3.0.1
photutils==2.3.0
reproject==0.14.1
scipy==1.17.1
toml==0.10.2
tqdm==4.66.5
```

### Installation Steps
1. Clone the repository:
   ```bash
   git clone https://github.com/user/GalfitX.git
   cd GalfitX
   ```

2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Install the package:
   ```bash
   pip install -e .
   ```

## Project Structure

The GalfitX package is organized into the following modules:

### Core Modules
- `galfitx_main.py`: Main pipeline entry point with `core_galfitx` function
- `source_detection.py`: Source detection and segmentation algorithms
- `create_setup_gs.py`: Setup generation for GalfitS
- `postage_stamp.py`: Image crop and stamp extraction utilities
- `mask.py`: Mask generation for neighboring objects
- `sky_calculation.py`: Background estimation algorithms
- `read_setup.py`: Configuration parsing utilities
- `gx_gsutils.py`: Utility functions for GalfitS integration
- `utils.py`: General utility functions
- `create_psf.py`: PSF generation utilities
- `model_isoflux.py`: Flux-conserving model utilities

### Additional Components
- `gxdemo.py`: Demo scripts demonstrating usage
- `gxdemo_parallel.py`: Parallel processing demonstration
- `csst_pipe.py`: CSST-specific pipeline components
- `read_sersic_results.py`: Result analysis and parsing
- `demo_*.py`: Various demonstration scripts

## Core Components

### Core Pipeline (`galfitx_main.py`)
The main entry point for the pipeline is the `core_galfitx` function:

```python
def core_galfitx(
    image_name: str = "/path/to/image",
    weight_name: str = "/path/to/weightimage",
    kernel_name: str = "/path/to/kernel",
    image_list: List[str] = ["/path/to/image"],
    outdir_list: List[str] = [],
    filter_list: List[str] = [],
    label_list: List[str] = [],
    psf_list: List[str] = [],
    zero_list: List[float] = [],
    config: str = "./csst.toml",
) -> int:
```

#### Parameters
- `image_name`: Path to detection image
- `weight_name`: Path to weight image
- `kernel_name`: Path to convolution kernel file
- `image_list`: Paths to multi-band science images
- `outdir_list`: Directories to store multi-band postage stamps
- `filter_list`: Filter identifiers for GalfitS
- `label_list`: Labels for each filter
- `psf_list`: Paths to multi-band input PSF images
- `zero_list`: Multi-band magnitude zeropoints
- `config`: Path to configuration file

### Source Detection (`source_detection.py`)
Implements advanced detection algorithms using SExtractor methodology with dual-mode and HDR (High Dynamic Range) variants:

- `SExtractor`: Basic SExtractor interface
- `SExtractor_dualmode`: Dual-mode detection algorithm
- `SExtractor_HDR`: High Dynamic Range detection with adaptive thresholds
- Comprehensive background estimation and cleaning functions

### Model Setup (`create_setup_gs.py`)
Generates configuration files for GalfitS with:
- Component parameter initialization
- PSF handling and processing
- Multi-band setup preparation

## Usage Examples

### Basic Usage
The primary usage pattern involves preparing inputs and calling the core pipeline:

```python
from galfitx import core_galfitx

result = core_galfitx(
    image_name="/path/to/detection_image.fits",
    weight_name="/path/to/weight_image.fits",
    kernel_name="/path/to/kernel.txt",
    image_list=["/path/to/band1.fits", "/path/to/band2.fits"],
    outdir_list=["./output/band1/", "./output/band2/"],
    filter_list=["F475W", "F814W"],
    label_list=["band1", "band2"],
    psf_list=["/path/to/psf1.fits", "/path/to/psf2.fits"],
    zero_list=[25.0, 25.0],
    config="./config.toml"
)
```

### Configuration File Format
The configuration file (`csst.toml`) should include:

```toml
detect_minarea_cold = 5
detect_minarea_hot = 5
detect_thresh_cold = 1.5
detect_thresh_hot = 2.0
deblend = true
deblend_nthresh_cold = 32
deblend_nthresh_hot = 32
deblend_mincont_cold = 0.005
deblend_mincont_hot = 0.005
clean = true
back_type = "AUTO"
back_value = 0.0
back_size_cold = 64
back_size_hot = 64
back_filtersize = 3
BACKGROUND = "WEIGHT"
scale_factor = 1.0
pixel_scale = 0.05
sizefac = 8
scale = 1.0
offset = 0
limgal = -1
b = 0.3
setup = "setup_file.conf"
```

## Configuration Files

### TOML Configuration
The pipeline uses TOML format for configuration with the following key parameters:
- Detection parameters (thresholds, areas, deblending)
- Background estimation parameters
- Measurement scaling factors
- Input/output path specifications

### Setup Files
Setup files define the model parameters for GalfitS fitting, including:
- Component types (Sersic, exponential, de Vaucouleurs)
- Parameter constraints and initial values
- Fitting options and convergence criteria

## Building Documentation Site

### Local Development
The project includes a Sphinx-based documentation system:

1. Install documentation dependencies:
   ```bash
   pip install -r docs/requirements.txt
   ```

2. Navigate to the docs directory:
   ```bash
   cd docs
   ```

3. Build documentation locally:
   ```bash
   make html
   ```

4. Open `_build/html/index.html` in your browser

### Documentation Source Files
Located in `docs/source/`:
- `index.rst`: Main documentation index
- `conf.py`: Sphinx configuration
- `galfitx.rst`: Auto-generated module documentation
- `modules.rst`: Module index

## Deployment

### GitHub Pages Setup
To deploy documentation via GitHub Pages:

1. Create a `gh-pages` branch or enable GitHub Pages in repository settings
2. Configure Sphinx to output to the appropriate directory (typically `docs/_build/html`)
3. Update the configuration in `docs/source/conf.py` with proper paths:
   ```python
   html_theme = 'sphinx_rtd_theme'
   html_static_path = ['_static']
   ```
4. Commit the built documentation to the appropriate branch

### Continuous Integration
Add documentation building to your CI pipeline:
```yaml
- name: Build documentation
  run: |
    cd docs
    make html
- name: Deploy to GitHub Pages
  uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./docs/_build/html
```

### Online Access
Once deployed, users can access the documentation at:
`https://<username>.github.io/<repository>/`

The documentation includes:
- API reference for all modules
- Installation instructions
- Usage examples
- Configuration guidelines
- Troubleshooting tips

## Contributing

We welcome contributions to improve the documentation and the software. Please fork the repository and submit pull requests with your enhancements.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For support and questions, please open an issue on the GitHub repository.