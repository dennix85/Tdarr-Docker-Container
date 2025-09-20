# Tdarr Docker Installation

## Quick Setup

Simply point your existing Docker setup to this image:
```bash
docker pull denix811/tdarr-denix
```

## Use With Your Existing Setup

**Docker Compose:** Change the image line in your compose file to:
```yaml
image: denix811/tdarr-denix
```

**Unraid Template:** Set Repository to:
```
denix811/tdarr-denix
```

**Docker Run:** Replace your existing image with:
```bash
denix811/tdarr-denix
```

## Required Variables to Add

Add these environment variables to your existing setup:

- `TDARR_MODE=server|node|both` (must be set)
- `GPU_VENDOR=intel|nvidia|amd|none` (must be set)

## Hardware Access to Add

**For Intel/AMD GPU users:** Add device mapping:
```
--device=/dev/dri:/dev/dri
```

**For NVIDIA GPU users:** Add runtime or GPU access:
```
--runtime=nvidia
```
or
```
--gpus all
```

## Optional Features You Can Add

**Prometheus Monitoring:**
- Environment: `ENABLE_TDARR_EXPORTER=true`
- Port: `9090:9090`

**Python Environment:**
- Environment: `PYTHON_PERSISTENT_VENV=true` 
- Volumes: 
  - `/path/to/pyvenv:/pyvenv`
  - `/path/to/pypayload:/pypayload`

**OCR Support:**
- Volume: `/path/to/tessdata:/tessdata`
