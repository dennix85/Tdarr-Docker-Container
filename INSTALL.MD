# Installation Instructions

## Quick Start

Pull the image:
```bash
docker pull denix811/tdarr-denix
```

## Integrate with Your Existing Setup

### If you already have Tdarr running:

**Docker Compose:** Update your `docker-compose.yml`:
```yaml
services:
  tdarr:
    image: denix811/tdarr-denix  # Change this line
    # Keep all your existing volumes, ports, etc.
    environment:
      - TDARR_MODE=both          # Add this
      - GPU_VENDOR=intel         # Add this (intel/nvidia/amd/none)
      # Keep your existing environment variables
```

**Unraid:** In your Tdarr template:
1. Change Repository to: `denix811/tdarr-denix`
2. Add required variables:
   - `TDARR_MODE` = `both` (or `server`/`node`)
   - `GPU_VENDOR` = `intel` (or `nvidia`/`amd`/`none`)

**Docker Run:** Replace your image name with `denix811/tdarr-denix` and add:
```bash
-e TDARR_MODE=both \
-e GPU_VENDOR=intel \
# Keep all your existing -v, -p, and other flags
```

## Hardware Acceleration Setup

**Intel/AMD GPU users:** Add this to your existing setup:
```bash
--device=/dev/dri:/dev/dri
```

**NVIDIA GPU users:** Add this to your existing setup:
```bash
--runtime=nvidia
```
*or*
```bash
--gpus all
```

## Optional Extras You Can Add

### Prometheus Monitoring
```bash
-e ENABLE_TDARR_EXPORTER=true
-p 9090:9090
```

### Python Scripting Environment
```bash
-e PYTHON_PERSISTENT_VENV=true
-v /path/to/pyvenv:/pyvenv
-v /path/to/pypayload:/pypayload
```

### OCR Support
```bash
-v /path/to/tessdata:/tessdata
```

## What You Get

This image includes the latest FFmpeg 8.0, HandBrake 1.10.2, full hardware acceleration support, Dolby Vision/HDR10+ tools, and many more features compared to the standard Tdarr image.

Your existing Tdarr configuration, databases, and media libraries will work exactly the same - just with better performance and more codec support.
