---
layout: post
title:  "Imagemagick"
lang: en
category: general
tags: imagemagick
comments: true
---

# ImageMagick Quick Reference: Essential Image Operations 🖼️

*Your go-to cheat sheet for common ImageMagick operations with both legacy and modern syntax*

## ⚠️ Legacy vs Modern Commands

**Legacy (ImageMagick v6):** `convert`
**Modern (ImageMagick v7):** `magick` or `magick convert`

> **Warning:** The `convert` command is deprecated in IMv7. Use `magick` instead of `convert` or `magick convert`

---

## 🔄 Basic Resize & Crop Operations

### Resize with Canvas Extension
```bash
# Legacy
convert out.png -resize 2516x1718 -gravity center -extent 3555x2000 out2.png

# Modern
magick out.png -resize 2516x1718 -gravity center -extent 3555x2000 out2.png
```
*Resizes image to fit within 2516x1718, then extends canvas to 3555x2000 centered*

### Smart Resize Options
```bash
# Resize maintaining aspect ratio
magick input.png -resize 800x600 output.png

# Force exact dimensions (may distort)
magick input.png -resize 800x600! output.png

# Resize only if larger
magick input.png -resize 800x600> output.png

# Resize only if smaller
magick input.png -resize 800x600< output.png
```

---

## 📐 Image Merging & Concatenation

### Horizontal Merge (Left-Right)
```bash
# Legacy
convert +append 1.png 2.png out.png

# Modern
magick 1.png 2.png +append out.png
```

### Vertical Merge (Top-Bottom)
```bash
# Legacy
convert -append 1.png 2.png out.png

# Modern
magick 1.png 2.png -append out.png
```

---

## 🎨 Adding Borders & Padding

### Basic Border
```bash
# Legacy
convert 1.png -bordercolor "#FFFFFF" -border 0x100 out2.png

# Modern
magick 1.png -bordercolor "#FFFFFF" -border 0x100 out2.png
```
*⚠️ Important: Settings like `-bordercolor` must come BEFORE the option that uses them (`-border`)*

### Advanced Border Options
```bash
# Different border sizes
magick input.png -bordercolor "#FF0000" -border 10x20 output.png

# Rounded corners with border
magick input.png -bordercolor black -border 5 -fill white -opaque black output.png
```

---

## 🔗 Advanced Merging Techniques

### Merge with Different Dimensions

#### 1. Resize to Match Height (Horizontal Merge)
```bash
# Get height of first image, resize second to match
magick identify -format "%h" 1.png > height.txt
magick 2.png -resize x$(cat height.txt) 2_resized.png
magick 1.png 2_resized.png +append merged.png
```

#### 2. Resize to Match Width (Vertical Merge)
```bash
# Get width of first image, resize second to match
magick identify -format "%w" 1.png > width.txt
magick 2.png -resize $(cat width.txt)x 2_resized.png
magick 1.png 2_resized.png -append merged.png
```

#### 3. One-liner Solutions
```bash
# Horizontal merge - resize all to same height (smallest)
magick 1.png 2.png -resize x600 +append output.png

# Vertical merge - resize all to same width (smallest)
magick 1.png 2.png -resize 800x -append output.png
```

### Merge with Padding/Spacing
```bash
# Add 20px white space between images horizontally
magick 1.png \( -size 20x1 xc:white \) 2.png +append output.png

# Add 20px transparent space between images vertically
magick 1.png \( -size 1x20 xc:transparent \) 2.png -append output.png

# Add colored separator
magick 1.png \( -size 5x1 xc:"#CCCCCC" \) 2.png +append output.png
```

### Smart Auto-Resize Merging
```bash
# Horizontal merge with auto-height matching
magick 1.png 2.png -background white -gravity center -extent 0x+0+0 +append output.png

# Vertical merge with auto-width matching
magick 1.png 2.png -background white -gravity center -extent +0+0x0 -append output.png
```

---

## 🎯 Precise Positioning & Alignment

### Custom Positioning
```bash
# Place second image at specific coordinates on first
magick 1.png 2.png -geometry +100+50 -composite output.png

# Center second image on first
magick 1.png 2.png -gravity center -composite output.png
```

### Grid Layouts
```bash
# 2x2 grid
magick 1.png 2.png +append row1.png
magick 3.png 4.png +append row2.png
magick row1.png row2.png -append grid.png

# Clean up
rm row1.png row2.png
```

---

## 🛠️ Utility Commands

### Get Image Information
```bash
# Basic info
magick identify input.png

# Specific dimensions
magick identify -format "%wx%h" input.png

# Detailed info
magick identify -verbose input.png
```

### Batch Operations
```bash
# Resize all PNGs in directory
magick mogrify -resize 800x600 *.png

# Convert format for all images
magick mogrify -format jpg *.png
```

---

## 📝 Pro Tips & Best Practices

### 1. Order Matters
```bash
# ❌ Wrong - settings after the operation
magick input.png -border 10 -bordercolor red output.png

# ✅ Correct - settings before the operation
magick input.png -bordercolor red -border 10 output.png
```

### 2. Background Colors for Transparency
```bash
# Set background color for operations that might create transparency
magick input.png -background white -flatten output.png
```

### 3. Memory Management for Large Images
```bash
# Limit memory usage
magick -limit memory 1GB input.png -resize 50% output.png
```

### 4. Quality Control
```bash
# Set JPEG quality
magick input.png -quality 90 output.jpg

# PNG compression level
magick input.png -define png:compression-level=9 output.png
```

---

## 🔄 Migration Helper

### Quick Command Conversion
Replace your old commands:
```bash
# Old way (v6)
convert image.png -resize 800x600 output.png

# New way (v7)
magick image.png -resize 800x600 output.png
```

### Batch Script Migration
```bash
#!/bin/bash
# Replace 'convert' with 'magick' in all your scripts
sed -i 's/^convert /magick /' your_script.sh
```

---

## 📚 Common Use Cases Summary

| Task | Legacy Command | Modern Command |
|------|----------------|----------------|
| Resize | `convert in.png -resize 800x600 out.png` | `magick in.png -resize 800x600 out.png` |
| Horizontal merge | `convert +append 1.png 2.png out.png` | `magick 1.png 2.png +append out.png` |
| Vertical merge | `convert -append 1.png 2.png out.png` | `magick 1.png 2.png -append out.png` |
| Add border | `convert in.png -bordercolor white -border 10 out.png` | `magick in.png -bordercolor white -border 10 out.png` |
| Canvas extend | `convert in.png -gravity center -extent 1920x1080 out.png` | `magick in.png -gravity center -extent 1920x1080 out.png` |

---

*Keep this reference handy for quick ImageMagick operations! Remember: settings before operations, and embrace the new `magick` command for future-proof scripts.* ✨
