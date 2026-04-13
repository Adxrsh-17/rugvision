# rugvision

**Automatic rug placement on room images using computer vision and the Segment Anything Model (SAM).**

rugvision detects floor regions in room photos, finds the optimal placement zone, warps a rug image into perspective, and blends it seamlessly into the scene — producing a realistic preview of how a rug would look in any room.

---

## Features

- Floor region detection using SAM with a refinement pipeline
- Optimal placement zone computed via distance transform
- Perspective warping for realistic rug orientation
- Alpha-mask blending for seamless compositing
- Enhanced Room 3 workflow with furniture exclusion
- Batch and single-image processing modes
- Debug collages for step-by-step inspection

---

## Tech Stack

| Tool | Purpose |
|---|---|
| Python 3.8+ | Core language |
| OpenCV | Image processing |
| NumPy | Numerical operations |
| PyTorch | Model inference backend |
| Segment Anything Model (SAM) | Floor segmentation |

---

## Project Structure

```
rugvision/
├── run_assignment.py         # Main entry point
├── requirements.txt          # Python dependencies
├── rug_visualizer.ipynb      # Reference notebook
├── src/
│   └── rug_visualizer/
│       ├── __init__.py
│       ├── floor_detection.py
│       ├── placement.py
│       └── pipeline.py
└── rug-ai/
    ├── images/               # Input room and rug images
    ├── models/               # SAM checkpoint
    └── outputs/              # Generated results
```

---

## Setup

**1. Clone the repo and create a virtual environment**

```bash
git clone https://github.com/Adxrsh-17/rugvision.git
cd rugvision
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
```

**2. Install dependencies**

```bash
pip install -r requirements.txt
```

**3. Download the SAM checkpoint**

```bash
wget https://dl.fbaipublicfiles.com/segment_anything/sam_vit_h_4b8939.pth -P rug-ai/models/
```

> GPU is strongly recommended for SAM inference. CPU mode is supported but significantly slower.

---

## Usage

**Batch mode** — process all room/rug combinations:

```bash
python run_assignment.py --mode batch
```

**Single mode** — process one room with one rug:

```bash
python run_assignment.py --mode single --room room1.jpeg --rug rug1.jpg
```

**Enhanced Room 3 pipeline** — includes furniture-aware exclusion:

```bash
python run_assignment.py --mode room3-enhanced --room room3.jpeg --rug rug3.jpg
```

**Disable debug collages:**

```bash
python run_assignment.py --mode batch --no-debug
```

---

## Output Files

All outputs are written to `rug-ai/outputs/`:

| File | Description |
|---|---|
| `roomN_rugN_result.jpg` | Final composited image |
| `roomN_rugN_steps.jpg` | Step-by-step debug collage |
| `room3_*_enhanced.jpg` | Room 3 enhanced result |
| `room3_*_enhanced_steps.jpg` | Room 3 debug collage |
| `_gallery.jpg` | Combined gallery of all results |

---

## How It Works

**Floor detection** — SAM segments the room image and a refinement pipeline isolates the floor region, excluding walls, furniture, and other surfaces.

**Placement optimization** — A distance transform finds the point farthest from all floor boundaries, giving the most stable and centered placement zone:

$$D(x,y) = \min_{(i,j) \in \partial\Omega} \sqrt{(x-i)^2 + (y-j)^2}$$

**Perspective warping** — A homography maps the rug into the floor plane to match the room's perspective:

$$\mathbf{x'} = H\mathbf{x}$$

**Alpha compositing** — The warped rug is blended with the room image using a soft alpha mask for a seamless, realistic result:

$$I_{\text{final}}(p) = \alpha(p)\,I_{\text{rug}}(p) + (1 - \alpha(p))\,I_{\text{room}}(p)$$

---

## Notes

- Large input images are automatically downsampled for stability
- The Jupyter notebook (`rug_visualizer.ipynb`) is kept as a reference; the script workflow is recommended for actual use
- GPU significantly improves SAM inference speed; CPU is supported as a fallback

---

## License

MIT License — see [LICENSE](LICENSE) for details.
