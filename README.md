# Tdarr with Full Hardware Acceleration Latest Handbrake 1.10.1 and FFmpeg 8.0 Dolby Vision and HDR10(+) and OCR tools 

> **🚀 Coming Soon - GitHub Repository!**  
> A dedicated GitHub repository with pre-designed workflows, custom plugins, and comprehensive documentation is currently in development and will be available very soon! This will include ready-to-use Tdarr flows for common scenarios and advanced custom plugins to supercharge your media processing. Stay tuned for the link announcement! 📦✨

A comprehensive Docker image for **Tdarr Latest** with complete hardware acceleration support for Intel QSV, NVIDIA NVENC/NVDEC, and AMD AMF. Built from source with the latest FFmpeg, HandBrake, and media processing tools, plus integrated Prometheus monitoring and optional persistent Python environment.

## 🚀 Quick Start

Use your existing Tdarr Docker Compose, run commands, or Unraid templates. Simply add these required configurations:

**Required Environment Variables:**
- `TDARR_MODE=server|node|both` *(must be set)*
- `GPU_VENDOR=intel|nvidia|amd|none` *(must be set)*

**Hardware Access:**
- `--device=/dev/dri:/dev/dri` *(Intel/AMD GPU)*
- `--runtime=nvidia` or `--gpus all` *(NVIDIA GPU)*

**Optional Monitoring:**
- `ENABLE_TDARR_EXPORTER=true` *(Prometheus metrics)*
- `-p 9090:9090` *(metrics endpoint)*

**Optional Python Environment:**
- `PYTHON_PERSISTENT_VENV=true` *(persistent Python environment)*
- `-v /path/to/pyvenv:/pyvenv` *(Python virtual environment storage)*
- `-v /path/to/pypayload:/pypayload` *(user scripts and payloads)*

**Optional Volume for OCR:**
- `-v /path/to/tessdata:/tessdata` *(Tesseract language data)*

## 🎯 Key Features

### **Hardware Acceleration**
- **Intel QSV** - 12th gen+ with AV1 encode/decode (12th.13th.14th Only decode)
- **NVIDIA NVENC/NVDEC** - H.264, H.265, AV1 support
- **AMD AMF** - Hardware encoding for Radeon GPUs
- **VA-API** - Intel GPU acceleration on Linux

### **Latest Media Tools** (Source Compiled)
- **FFmpeg 8.0** - Latest codec support with Vulkan acceleration
- **HandBrake 1.10.2** - Hardware-accelerated transcoding with Dolby Vision support
- **MediaInfo** - Complete media analysis
- **MKVToolNix 94.0** - MKV container handling
- **MP4Box** - MP4 container manipulation

### **Advanced Features**
- **Dolby Vision** support with dovi_tool 2.3.1
- **HDR10+** processing with hdr10plus_tool 1.7.1
- **Intel Arc GPU** support via oneVPL 2.15.0
- **SVT-AV1** encoder for best quality
- **MongoDB** integration with mongosh 2.5.6
- **OCR** support with Tesseract
- **Prometheus Monitoring** with Tdarr Exporter 1.4.3
- **Persistent Python Environment** with user script support
- **Whisper AI** transcription capabilities
- **WebP/JPEG XL/AVIF** next-generation image format support

## 📋 Configuration

### **Required Environment Variables**
- `TDARR_MODE` **(REQUIRED)** - Set to `server`, `node`, or `both`
- `GPU_VENDOR` **(REQUIRED)** - Set to `amd`, `intel`, `nvidia`, or `none`

### **Optional Python Environment Configuration**
- `PYTHON_PERSISTENT_VENV` - Enable persistent Python virtual environment (default: `false`)

When `PYTHON_PERSISTENT_VENV=true`, both volumes become **mandatory**:
- `-v /host/path/to/pyvenv:/pyvenv` - Python virtual environment storage
- `-v /host/path/to/pypayload:/pypayload` - User scripts and payloads directory

### **Optional Monitoring Configuration**
- `ENABLE_TDARR_EXPORTER` - Enable Prometheus metrics (default: `false`)
- `TDARR_EXPORTER_PROMETHEUS_PORT` - Metrics port (default: `9090`)
- `TDARR_EXPORTER_PROMETHEUS_PATH` - Metrics endpoint (default: `"/metrics"`)
- `TDARR_EXPORTER_URL` - Tdarr Server URL (default: `"http://localhost:8265"`)
- `TDARR_EXPORTER_API_KEY` - API token for authenticated instances
- `TDARR_EXPORTER_LOG_LEVEL` - Log verbosity (default: `"info"`)
- `TDARR_EXPORTER_HTTP_MAX_CONCURRENCY` - Max HTTP requests (default: `3`)
- `TDARR_EXPORTER_VERIFY_SSL` - SSL verification (default: `true`)

### **Hardware Access**
```bash
# Intel/AMD GPU access
--device=/dev/dri:/dev/dri

# NVIDIA GPU access  
--runtime=nvidia
# AND
- NVIDIA_VISIBLE_DEVICES=all
- NVIDIA_DRIVER_CAPABILITIES=all
```

### **Port Mapping**
```bash
-p 8265:8265   # Tdarr Server Web UI
-p 8266:8266   # Tdarr Node (if running node)
-p 9090:9090   # Tdarr Exporter metrics (optional)
```

## 🔧 Usage Examples

### Basic Server with Monitoring and Python Environment
```bash
docker run -d \
  --name=tdarr-server \
  -e TDARR_MODE=server \
  -e GPU_VENDOR=amd \
  -e ENABLE_TDARR_EXPORTER=true \
  -e PYTHON_PERSISTENT_VENV=true \
  --device=/dev/dri:/dev/dri \
  -p 8265:8265 \
  -p 9090:9090 \
  -v /path/to/configs:/app/configs \
  -v /path/to/logs:/app/logs \
  -v /path/to/media:/media \
  -v /path/to/pyvenv:/pyvenv \
  -v /path/to/pypayload:/pypayload \
  your-tdarr-image
```

### Docker Compose with Full Features
```yaml
version: '3.8'
services:
  tdarr:
    image: your-tdarr-image
    container_name: tdarr
    environment:
      - TDARR_MODE=both
      - GPU_VENDOR=intel
      - ENABLE_TDARR_EXPORTER=true
      - PYTHON_PERSISTENT_VENV=true
      - TDARR_EXPORTER_LOG_LEVEL=info
    devices:
      - /dev/dri:/dev/dri
    runtime: nvidia  # Remove if not using NVIDIA
    ports:
      - "8265:8265"
      - "8266:8266" 
      - "9090:9090"
    volumes:
      - ./configs:/app/configs
      - ./logs:/app/logs
      - /path/to/media:/media
      - /path/to/tessdata:/tessdata
      - ./pyvenv:/pyvenv
      - ./pypayload:/pypayload
```

### Node-Only Configuration (CPU Processing)
```yaml
version: '3.8'
services:
  tdarr-node:
    image: your-tdarr-image
    container_name: tdarr-node-cpu
    environment:
      - TDARR_MODE=node
      - GPU_VENDOR=none
      - PYTHON_PERSISTENT_VENV=true
    ports:
      - "8266:8266"
    volumes:
      - ./logs:/app/logs
      - /path/to/media:/media
      - ./pyvenv:/pyvenv
      - ./pypayload:/pypayload
```

### External Prometheus Configuration
```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'tdarr-exporter'
    static_configs:
      - targets: ['your-docker-host:9090']
    scrape_interval: 30s
```

## 🐍 Python Environment Features

When `PYTHON_PERSISTENT_VENV=true`:

### **Automatic Setup**
- Creates persistent Python virtual environment at `/pyvenv`
- User scripts directory at `/pypayload` (auto-added to PYTHONPATH)
- Full package installation capabilities with `pip`
- Environment persists across container restarts

### **Usage Examples**
```bash
# Install packages (persists across restarts)
docker exec -it tdarr-container pip install requests beautifulsoup4 opencv-python

# Run your scripts
docker exec -it tdarr-container python /pypayload/my_script.py

# Interactive Python shell with access to all packages
docker exec -it tdarr-container python
```

### **Script Development**
Place your Python scripts in the `/pypayload` directory on your host:
```python
# /pypayload/media_processor.py
import requests
import json
from pathlib import Path

# Your custom media processing logic here
# Can interact with Tdarr API, process files, etc.

def process_media_files():
    # Example: Get Tdarr statistics
    response = requests.get('http://localhost:8265/api/v2/statistics')
    stats = response.json()
    print(f"Queue size: {stats.get('queue_size', 0)}")

if __name__ == "__main__":
    process_media_files()
```

## 🔍 Hardware Verification

Check what hardware acceleration and features are available:
```bash
docker exec -it tdarr-container /usr/local/bin/check-hw-sw.sh
```

## 📱 Access Points

- **Tdarr Web UI**: `http://localhost:8265`
- **Health Check**: `http://localhost:8265/api/v2/status`
- **Prometheus Metrics**: `http://localhost:9090/metrics` *(if exporter enabled)*

## 🎬 Optimized for Tdarr Plugins

This image is specifically optimized for creating and maintaining **Tdarr Plugins** with:
- All major codecs and containers supported
- Hardware acceleration APIs available
- Latest tool versions for maximum compatibility
- Pre-configured paths and permissions
- WebP support (fixes MongoDB plugin issues)
- Python scripting environment for advanced automation

Perfect for developers working on Tdarr plugins in JavaScript/TypeScript or Python automation scripts.

## 📊 What's Included

### **🔨 Compiled from Source** (Latest Versions)
| Component | Version | Purpose |
|-----------|---------|---------|
| **FFmpeg** | 8.0 | Media encoding/decoding with full HW acceleration + Vulkan |
| **HandBrake** | 1.10.2 | Video transcoding with QSV/VCE/NVENC + Dolby Vision support |
| **MKVToolNix** | 94.0 | MKV container tools (mkvmerge, mkvinfo, etc.) |
| **MP4Box (GPAC)** | v2.4.0 | MP4 container manipulation |
| **SVT-AV1** | 3.1.2 | High-quality AV1 encoder |
| **MediaInfo/MediaInfoLib** | Latest | Complete media file analysis |
| **ZenLib** | Latest | Media analysis library dependency |
| **ExifTool** | 13.33 | Metadata extraction/manipulation |
| **libva** | 2.22.0 | VA-API library for Intel GPU |
| **Intel VAAPI Driver** | 2.4.1 | Intel GPU hardware decode driver |
| **Intel Media Driver** | 25.2.6 | Modern Intel GPU driver (iHD) |
| **Intel GMM Library** | 22.8.1 | Intel GPU memory management |
| **oneVPL** | 2.15.0 | Intel Video Processing Library |
| **oneVPL GPU Runtime** | 25.2.6 | Intel Arc GPU runtime support |
| **Whisper.cpp** | Latest | AI transcription capabilities |
| **libplacebo** | Latest | GPU-accelerated video processing |
| **Vulkan Headers/Loader** | Latest | Vulkan graphics API support |

### **📦 Pre-built Binaries** (Latest Releases)
| Tool | Version | Purpose |
|------|---------|---------|
| **dovi_tool** | 2.3.1 | Dolby Vision metadata processing |
| **hdr10plus_tool** | 1.7.1 | HDR10+ metadata processing |
| **MongoDB Shell** | 2.5.6 | Database operations and management |
| **Tdarr** | 2.17.01 | Main application (Server + Node) |
| **Tdarr Exporter** | 1.4.3 | Prometheus metrics exporter |
| **amdgpu_top** | Latest | AMD GPU monitoring tool |

### **📋 Runtime System Tools** (Available in Container)
#### **Media & Processing Tools**
- **Tesseract OCR** - Optical character recognition with multiple languages
- **ccextractor** - Closed caption extraction  
- **zstd** - High-performance compression
- **vainfo** - VA-API information and testing
- **Python 3.12** - Full scripting environment with venv support
- **pipx** - Isolated Python application installer
- **rsync** - File synchronization
- **AsciiDoc & Pandoc** - Documentation processing
- **MC** - CLI File Manager with GUI
- **radeontop** - AMD GPU monitoring
- **intel-gpu-tools** - Intel GPU utilities

#### **Hardware Acceleration Libraries**
- **Intel**: intel-media-va-driver, libva runtime libraries
- **AMD**: mesa-va-drivers, radeonsi drivers
- **OpenCL**: ocl-icd-libopencl1 (runtime)
- **Graphics**: libdrm2, libpciaccess0, libwayland runtime
- **Vulkan**: Complete Vulkan development stack

## 🆘 Troubleshooting

### **Container won't start**
- Ensure `TDARR_MODE` is set to `server`, `node`, or `both`
- Ensure `GPU_VENDOR` is set to `amd`, `intel`, `nvidia`, or `none`
- Check hardware device permissions: `ls -la /dev/dri`

### **Python environment issues**
- When `PYTHON_PERSISTENT_VENV=true`, both `/pyvenv` and `/pypayload` volumes are required
- Check volume mount permissions: `docker exec container-name ls -la /pyvenv /pypayload`
- Verify volumes are properly mounted as persistent storage

### **No hardware acceleration**
- Verify GPU drivers are installed on host
- Check device mapping: `--device=/dev/dri:/dev/dri`
- For NVIDIA: Ensure `nvidia-container-runtime` is installed
- Run hardware verification: `docker exec container-name /usr/local/bin/check-hw-sw.sh`

### **Tdarr Exporter issues**
- Exporter requires Tdarr Server to be running
- Check metrics endpoint: `curl http://localhost:9090/metrics`
- Verify Tdarr Server API is accessible

### **Permission issues**
- All files created with 777 permissions by default
- Run hardware check: `docker exec container-name /usr/local/bin/check-hw-sw.sh`

## 📝 Credits & Licensing

This Docker image integrates multiple open-source projects. All rights belong to their respective authors and maintainers:

### **Core Applications**
- **[Tdarr](https://github.com/HaveAGitGat/Tdarr)** - Main application by HaveAGitGat (licensed under various terms)
- **[Tdarr Exporter](https://github.com/homeylab/tdarr-exporter)** - Prometheus metrics by HomeyLab (licensed under Apache 2.0)

### **Media Processing Tools**
- **[FFmpeg](https://github.com/FFmpeg/FFmpeg)** - Cross-platform media solution (licensed under LGPL/GPL)
- **[HandBrake](https://github.com/HandBrake/HandBrake)** - Video transcoder (licensed under GPL v2)
- **[MediaInfo](https://github.com/MediaArea/MediaInfo)** - Media analysis tool (licensed under BSD-2-Clause)
- **[MKVToolNix](https://github.com/mbunkus/mkvtoolnix)** - Matroska tools (licensed under GPL v2)
- **[MP4Box/GPAC](https://github.com/gpac/gpac)** - Multimedia framework (licensed under LGPL)
- **[SVT-AV1](https://github.com/AOMediaCodec/SVT-AV1)** - AV1 encoder by Intel/Netflix (licensed under BSD+Patent)
- **[Whisper.cpp](https://github.com/ggml-org/whisper.cpp)** - AI transcription by Georgi Gerganov (licensed under MIT)

### **Specialized Tools**
- **[dovi_tool](https://github.com/quietvoid/dovi_tool)** - Dolby Vision processing by quietvoid (licensed under MIT)
- **[hdr10plus_tool](https://github.com/quietvoid/hdr10plus_tool)** - HDR10+ processing by quietvoid (licensed under MIT)
- **[ExifTool](https://github.com/exiftool/exiftool)** - Metadata extraction by Phil Harvey (licensed under Artistic/GPL)
- **[MongoDB Shell](https://github.com/mongodb-js/mongosh)** - Database shell by MongoDB Inc.
- **[Tesseract](https://github.com/tesseract-ocr/tesseract)** - OCR engine by Google (licensed under Apache 2.0)
- **[CCExtractor](https://github.com/CCExtractor/ccextractor)** - Subtitle extraction (licensed under GPL)
- **[libplacebo](https://github.com/haasn/libplacebo)** - GPU video processing by Niklas Haas (licensed under LGPL)

### **Hardware Acceleration Libraries**
- **[Intel Media Driver](https://github.com/intel/media-driver)** - Intel GPU driver (licensed under MIT/BSD)
- **[Intel VAAPI Driver](https://github.com/intel/intel-vaapi-driver)** - Legacy Intel GPU support (licensed under MIT)
- **[oneVPL](https://github.com/oneapi-src/oneVPL)** - Intel Video Processing Library (licensed under MIT)
- **[libva](https://github.com/intel/libva)** - VA-API implementation (licensed under MIT)
- **[Vulkan Headers/Loader](https://github.com/KhronosGroup/Vulkan-Headers)** - Vulkan API by Khronos Group (licensed under Apache 2.0)

### **System Tools**
- **[zstd](https://github.com/facebook/zstd)** - Compression by Facebook (licensed under BSD)
- **[rsync](https://github.com/WayneD/rsync)** - File synchronization (licensed under GPL v3)
- **[AsciiDoc](https://github.com/asciidoc-py/asciidoc-py)** - Documentation processing (licensed under GPL v2)
- **[MC](https://github.com/MidnightCommander/mc)** - CLI File Manager with GUI (licensed under GPL v3)
- **[Pandoc](https://github.com/jgm/pandoc)** - Universal document converter (licensed under GPL v2)

### **Licensing Notice**
This Docker image is provided as-is and incorporates multiple open-source projects, each with their own licensing terms. Users are responsible for ensuring compliance with all applicable licenses when using this image in production environments. The image creator makes no claims of ownership over the included software components.

For commercial use, please review the individual license terms of each component, particularly:
- FFmpeg (may require licensing for some codecs in commercial use)
- HandBrake (GPL v2 - source code availability requirements)
- Some hardware acceleration libraries may have specific terms

### **Disclaimer**
This is an unofficial Docker image created for convenience and enhanced functionality. It is not affiliated with or endorsed by the original Tdarr project or any of the included software projects. All trademarks belong to their respective owners.

## 🔗 Official Links

- **[Tdarr Official](https://tdarr.io)** - Main website
- **[Tdarr GitHub](https://github.com/HaveAGitGat/Tdarr)** - Source code
- **[Tdarr Discord](https://discord.com/invite/nhpv8K5)** - Community support
- **[Tdarr Docker Hub](https://hub.docker.com/r/haveagitgat/tdarr)** - Official Docker images
- **[Hardware Transcoding Guide](https://docs.tdarr.io/docs/installation/docker/hardware-transcoding)** - Official documentation
- **[Plugin Development](https://docs.tdarr.io/docs/plugins)** - Plugin creation guide
- **[Tdarr Exporter](https://github.com/homeylab/tdarr-exporter)** - Prometheus monitoring
- **[Prometheus](https://prometheus.io/)** - Monitoring system

## 🏷️ Version Information

- **Container Version**: Enhanced build with monitoring and Python scripting support
- **Base Distribution**: Ubuntu 24.04 LTS
- **Architecture**: linux/amd64
- **Last Updated**: Latest builds from source with FFmpeg 8.0 and HandBrake 1.10.1
- **Build Method**: Multi-stage compilation with optimization flags
- **Special Features**: Persistent Python environment, hardware auto-detection, comprehensive monitoring
