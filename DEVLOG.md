# 📓 Dev Log — Robotics & AI Journey

A daily log of what I built, learned, and struggled with.
Newest entries at the top.

---

## Day 14 — August 28, 2026

**Phase:** 2 — AI & Perception (Deep Learning mini-arc, Day 1 of 5)
**Time spent:** ~2.5 hrs

### ✅ What I did

- Watched 3Blue1Brown Neural Networks series (videos 1-4): architecture, gradient descent, backpropagation intuition, backprop calculus
- Watched CS231n Lecture 3 (Loss Functions and Optimization) and Lecture 4 (Neural Networks and Backpropagation)
- Built `day14 nn fundamentals.ipynb` in new `phase2-ai-perception/deep-learning/` folder
- Built a non-linearly-separable toy dataset (interleaving two-crescent "moons" shape)
- Implemented a full 2-layer network from scratch in NumPy: forward pass (linear + ReLU + sigmoid), binary cross-entropy loss, backpropagation via manual chain rule, gradient descent training loop
- Trained a 4-hidden-neuron network — plateaued hard at 85% accuracy
- Diagnosed the plateau by visualizing the decision boundary + checking per-neuron activation stats — found 2 of 4 neurons permanently dead (0% activation, dying ReLU)
- Fixed via two separate experiments to isolate cause: (1) increased hidden neurons 4→16 with plain ReLU → 99.5% accuracy; (2) kept 4 neurons but switched to Leaky ReLU → only 86% accuracy, despite zero dead neurons
- Concluded capacity (hidden layer width) was the dominant constraint, not just dying ReLU — Leaky ReLU fixed neuron death but couldn't compensate for too few neurons overall

### 🧠 What I learned

- A network layer is linear transform (Wx+b) + nonlinear activation; without nonlinearity, stacking layers collapses into one linear function
- For sigmoid output + binary cross-entropy loss, dL/dz simplifies exactly to (prediction - truth) — a real calculus result, not an approximation
- Backprop is the chain rule applied recursively, layer by layer, reusing cached forward-pass values (z1, a1, z2, a2) — same idea as a computational graph
- Dying ReLU: if a neuron's weighted input is negative for every sample, ReLU outputs 0, gradient is 0, and gradient descent can never revive it — permanent, not recoverable by more training
- Each ReLU neuron contributes one "fold" (piecewise-linear segment) to a decision boundary; enough folds can approximate a smooth curve even though every individual piece is a straight line
- Leaky ReLU prevents neuron death (small negative slope keeps gradient nonzero) but doesn't fix insufficient network capacity — these are two distinct failure modes that can co-occur
- Weight initialization scale directly explains why untrained predictions cluster near 0.5 rather than being "randomly" spread 0-1

### 🚧 Blockers & how I fixed them

- 4-neuron network plateaued at 85% accuracy for 2500+ epochs with no improvement — investigated via decision boundary plot + per-neuron activation stats instead of just increasing epochs blindly
Found 2/4 neurons permanently dead (dying ReLU). Fixed with wider network (16 neurons) as primary fix; tested Leaky ReLU on the original 4-neuron network as a controlled comparison, confirming capacity was the larger constraint

### 🔜 Next session

- Day 15: Convolution as a learned operation — direct callback to Day 11's hand-coded Sobel kernels, but with learned weights instead of fixed ones
- Compare manual 2D convolution implementation to torch.nn.Conv2d

---

## Day 13 — July 17, 2026

**Phase:** 2 — AI & Perception 
**Time spent:** ~2 hrs

### ✅ What I did

- Watched First Principles of CV (Nayar) — SIFT Detector playlist (videos 12-16): interest points, blob detection, SIFT detector, SIFT descriptor
- Built `day13-sift-detector.ipynb`
- Built synthetic scale-space by hand (Gaussian blur at σ = 1,2,4,8,16) on a test image with 4 blobs of different sizes — visually confirmed small blobs dissolve faster than large ones under blur
- Computed Difference of Gaussians (DoG) between adjacent scale-space layers — visually confirmed each blob's peak response shifts from early DoG layers (small blob) to later layers (large blob)
- Ran `cv2.SIFT_create()` on the synthetic blob image, verified keypoint `size` scales correctly with actual blob radius (15→19.3, 30→38.3, 45→57.0, 60→72.3)
- Investigated why each blob produced 4-8 near-duplicate keypoints — traced to orientation histogram having multiple comparable peaks on radially symmetric shapes
- Filtered low-confidence keypoints (rasterization artifacts from `cv2.circle`) using `kp.response` threshold
- Tested SIFT on a real photo (Day 12's train track image) — generated a rotated (35°) + scaled (0.7x) version, ran keypoint matching with BFMatcher + Lowe's ratio test (0.75)
- Got 1030 good matches out of 3146 keypoints — mostly coherent, but visible crisscrossing especially in gravel/tree-canopy regions

### 🧠 What I learned

- Scale-space = same image blurred at increasing σ; no single blur level is correct for every blob size — small blobs dissolve early, large blobs dissolve late
- DoG (Difference of Gaussians) = subtracting adjacent blur layers, a cheap approximation of Laplacian-of-Gaussian blob detection — each blob peaks in DoG at the layer matching its own size
- SIFT keypoints carry position, size (scale), angle (orientation), and response (confidence) — size is literally the σ at which the DoG extremum was found
- Orientation assignment (dominant gradient direction near a keypoint) is what makes SIFT rotation-invariant; radially symmetric shapes produce multiple near-equal orientation peaks → multiple keypoints at the same location
- SIFT descriptor = 4×4 grid of 8-bin gradient histograms around a keypoint = 128 values, not a single number
- Lowe's ratio test (best match must be clearly better than second-best, e.g. <0.75x) filters ambiguous matches — same threshold-tuning pattern as Canny/Harris/Hough
- Ratio test alone isn't enough for repetitive texture (gravel, foliage) — locally ambiguous patches can still individually pass the ratio test while being globally wrong matches

### 🚧 Blockers & how I fixed them

- `from skimage import data` failed with ModuleNotFoundError — package not installed in local `robotics` conda env (was available in a different sandbox environment)
Fixed by reusing Day 12's train track photo instead of adding a new dependency
- 41 keypoints detected on a 4-blob synthetic image instead of ~4 — investigated via response values instead of assuming a bug; found real blob-center detections (response ≈0.16-0.17) clearly separable from low-confidence rasterization-edge artifacts (response ≈0.06) — confirmed synthetic circle edges aren't perfectly smooth at pixel level

### 🔜 Next session

- Add RANSAC + homography filtering to the real-photo matching pipeline — should collapse the crisscrossing false matches (repetitive gravel/tree texture) down to geometrically consistent true matches
- Move toward Phase 3 planning (ROS2, hardware) or continue deepening Phase 2 feature-matching work — decide next session

---

## Day 12 — July 11, 2026

**Phase:** 2 — AI & Perception 
**Time spent:** ~2 hrs

### ✅ What I did

- Watched First Principles of CV (Nayar) — Boundary Detection playlist (videos 7-11): line/curve fitting, active contours, Hough Transform, generalized Hough
- Built `day12-hough-transform.ipynb`
- Coded Harris Corner Detection (carried over from Day 11) — ran on 4-quadrant test image, correctly flagged the center 4-way intersection as strongest corner
- Built synthetic test image (horizontal, vertical, diagonal lines + rectangle) with known ground-truth line count
- Implemented Hough Transform pipeline — Canny → `cv2.HoughLinesP` — on synthetic image
- Debugged a line-count mismatch (14-15 detected vs 7 drawn) by printing each detected line's actual (x,y) endpoints and angle instead of guessing from the count alone
- Tested full pipeline on a real photo (own train track photo) — tree canopy texture caused massive over-detection (420 lines)
- Iteratively fixed real-photo detection: tightened Canny/Hough thresholds (420→234), then added ROI masking — rectangular band failed (trees flank tracks at every row), trapezoid ROI matching rail perspective succeeded (234→91, all tree noise gone)

### 🧠 What I learned

- Harris Corner Detection: structure tensor M built from Ix/Iy gradients in a local window; eigenvalues of M distinguish flat regions (both small) vs edges (one large) vs corners (both large). R = det(M) - k·trace(M)² is the cheap proxy for eigenvalue analysis
- Hough Transform shifts the problem from image space to parameter space — every edge pixel votes for every line (ρ,θ) that could pass through it; peaks in the accumulator = detected lines
- Polar form ρ = x·cos(θ) + y·sin(θ) avoids the vertical-line singularity of y = mx + c
- threshold / minLineLength / maxLineGap in HoughLinesP is the same sensitivity tradeoff pattern as Canny's hysteresis and Harris's response threshold — recurring tension across all of classical CV
- Any drawn line has two physical edges (rising + falling gradient) — Canny always double-detects a single drawn line into a parallel pair, independent of line thickness
- Real image noise (tree texture) can't always be fixed by parameter tuning — sometimes the fix is spatial (Region of Interest masking), not sensitivity-based
- ROI shape has to match scene geometry — a flat rectangular cutoff failed since trees flank the track at every row, not just above one line; a trapezoid matching the rails' converging perspective worked

### 🚧 Blockers & how I fixed them

- Real photo (train tracks) initially returned 420 Hough lines — almost entirely tree canopy texture, not real structure
Fixed partially by tightening Canny (50,150→100,200) and Hough params (threshold 80→150, minLineLength 60→100, maxLineGap 80→15), which cut it to 234
- 234 was still dominated by the treeline silhouette (jagged-but-diagonal edge got over-detected as many near-duplicate lines) — rectangular ROI band (top 40% cutoff) didn't fix it since trees run alongside the tracks at every height, not just above a line
Fixed by switching to a trapezoid ROI matching the rails' actual perspective (wide at bottom, narrow at vanishing point) — cut to 91, zero tree contamination
- Remaining unsolved: 91 detections vs ~2-4 real lines inside the correct ROI — gravel/tie texture is legitimate texture inside a legitimate region, so masking can't remove it. Left open for later (Gaussian blur pre-Canny or morphological cleanup are candidate fixes)

### 🔜 Next session

- Day 13 — SIFT Detector (videos 12-16): interest points, blob detection, scale-invariant keypoints, descriptors
- Revisit gravel-texture-inside-ROI problem if time allows — ties into SIFT's scale-space idea (separating texture-scale from structure-scale features)

---

## Day 11 — July 7, 2026

**Phase:** 2 — AI & Perception 
**Time spent:** ~2 hrs

### ✅ What I did

- Watched First Principles of CV (Nayar) — Edge Detection & Corner Detection playlist (videos 1-6)
- Implemented Sobel edge detection — separate Gx/Gy kernels, combined via gradient magnitude
- Implemented Laplacian edge detection — second-order derivative, zero-crossing based
- Implemented Canny edge detector — Gaussian blur → Sobel gradients → non-max suppression → hysteresis thresholding
- Ran threshold sensitivity sweep on Canny (low/high pairs) on a synthetic 4-quadrant test image
- Built side-by-side comparison: Sobel vs Laplacian vs Canny on the same image

### 🧠 What I learned

- Edge = significant local discontinuity in image intensity
- Sobel = first-order derivative — Gx detects vertical edges, Gy detects horizontal edges, magnitude = sqrt(Gx²+Gy²)
- Laplacian = second-order derivative — omnidirectional, but detects zero-crossings, which shows up visually as "doubled" edges (positive + negative lobes both turn positive after abs())
- Canny isn't a separate algorithm from scratch — it's Sobel gradients + a decision pipeline (blur → gradient → thin → threshold-and-link)
- Canny's hysteresis thresholds (low, high) act as a hard discard rule — real edges with low contrast can get deleted entirely if the threshold is set too high, even though Sobel/Laplacian still show them faintly
- Corners are 2D interest points where 2+ edges meet — more distinctive than edges alone (setup for SIFT/feature detection next)

### 🚧 Blockers & how I fixed them

- Canny (low=50, high=150) completely dropped the edge around the darkest quadrant (near-black square against black background) — Sobel/Laplacian still caught it faintly since they don't threshold
Fixed by lowering thresholds (low=20, high=60 and below) until the low-contrast edge reappeared — confirmed root cause was gradient magnitude at that boundary never clearing the low_threshold bar

### 🔜 Next session

- Day 12 — SIFT Detector & Feature Detection (videos 7-16): keypoints, descriptors, feature matching
- Harris Corner Detection (watched but not yet coded — pending a dedicated cell/session)

---

## Day 10 — July 2, 2026
**Phase:** 2 — AI & Perception
**Time spent:** ~2 hrs

### ✅ What I did
- Watched Binary Images playlist (First Principles of CV)
- Implemented contour detection using cv2.findContours()
- Extracted geometric properties — area, perimeter, centroid, bounding box
- Implemented morphological operations — erosion, dilation, opening, closing
- Visualized all operations side by side

### 🧠 What I learned
- Contours are outlines of white regions in binary images
- cv2.moments() gives centroid (center of mass) of a contour
- Erosion shrinks white regions, dilation expands them
- Opening = erosion→dilation (removes noise)
- Closing = dilation→erosion (fills holes)
- Kernel size controls how aggressive the morphological operation is

### 🚧 Blockers & how I fixed them
- NameError on eroded — code was split across cells incorrectly
  Fixed by putting all morphological ops in one single cell

### 🔜 Next session
- Edge detection (Canny, Sobel)
- Feature detection (Harris corners, SIFT)

---

## Day 9 — June 30, 2026
**Phase:** 2 — AI & Perception
**Time spent:** ~2 hrs

### ✅ What I did
- Watched First Principles of CV — Image Sensing playlist
- Built pixel intensity histograms using cv2.calcHist()
- Analyzed histogram peaks matching test image colors
- Implemented thresholding for binary image segmentation
- Compared threshold=100 vs threshold=50 effects

### 🧠 What I learned
- Histograms show distribution of pixel brightness (0-255)
- Histogram peaks correspond to dominant colors/regions in image
- Thresholding: pixel > threshold → white(255), else → black(0)
- Lower threshold = more pixels pass = more white in output
- cv2.threshold() returns (ret_value, result_image) — use _ to discard unused value
- CCD/CMOS sensors, Bayer filters reconstruct color from monochromatic sensors

### 🚧 Blockers & how I fixed them
- None today — clean session

### 🔜 Next session
- Binary Images playlist (First Principles of CV)
- Edge detection basics
- Contour detection

---

## Day 8 — June 28, 2026
**Phase:** 2 — AI & Perception
**Time spent:** ~2 hrs

### ✅ What I did
- Watched First Principles of CV — Image Formation playlist (6 videos)
- Started Phase 2 OpenCV notebook
- Learned images are just numpy arrays (height x width x 3)
- Created images from scratch using np.zeros()
- Fixed BGR vs RGB issue using cv2.cvtColor()
- Saved and reloaded images with cv2.imwrite() and cv2.imread()
- Core operations — grayscale, resize, crop

### 🧠 What I learned
- Images are numpy arrays of dtype uint8 (values 0-255)
- OpenCV uses BGR, matplotlib uses RGB — always convert before display
- Grayscale removes the channel dimension (300,300,3) → (300,300)
- Cropping is just numpy slicing — img[y1:y2, x1:x2]
- Pinhole camera model maps 3D world to 2D image plane

### 🚧 Blockers & how I fixed them
- BGR vs RGB confusion — fixed with cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

### 🔜 Next session
- Image Sensing playlist (First Principles of CV)
- Pixel intensity, histograms, thresholding
- Edge detection basics

---

## Day 7 — June 28, 2026
**Phase:** 1 — Foundations Sprint ← COMPLETED ✅
**Time spent:** ~3 hrs

### ✅ What I did
- Built complete Phase 1 Mini Project — Robot Arm Simulator
- Combined all Phase 1 concepts into one clean simulator function
- Rotation matrices, quaternions, DH parameters, forward kinematics, Jacobian
- Dual visualization — arm plot + joint velocity bar chart
- Pushed final mini project to GitHub

### 🧠 What I learned
- R = T_final[:3, :3] extracts rotation, NOT T_final[:3, 3] (that's translation)
- Trace of rotation matrix = R[0,0] + R[1,1] + R[2,2] — used for quaternion conversion
- All Phase 1 concepts chain together naturally into one simulator
- Clean function design makes code reusable and readable

### 🚧 Blockers & how I fixed them
- IndexError in quaternion function — R was 1D (column) not 3x3 matrix
  Fixed: T_final[:3, 3] → T_final[:3, :3]

### 🔜 Next session
- Phase 2 begins — AI & Perception
- Computer Vision fundamentals (OpenCV)
- Deep learning intro (PyTorch CNNs)

---

## Day 6 — June 25, 2026
**Phase:** 1 — Foundations Sprint
**Time spent:** ~2 hrs

### ✅ What I did
- Watched CS223A Lecture 6 (Jacobian + Velocity Kinematics)
- Built 2x3 Jacobian matrix for 3-joint planar arm from scratch
- Used pseudoinverse (pinv) to compute joint velocities from desired end effector velocity
- Verified result — J @ q_dot matched desired velocity exactly
- Visualized arm with velocity arrow showing desired end effector direction

### 🧠 What I learned
- Jacobian maps joint velocities to end effector velocities: ẋ = J(q)q̇
- Pseudoinverse handles non-square matrices — picks minimum norm solution
- 3-joint arm in 2D is redundant — infinite joint solutions for one end effector velocity
- Inverse Jacobian is how real robot controllers work — runs thousands of times per second
- plt.arrow() draws velocity vectors on matplotlib plots

### 🚧 Blockers & how I fixed them
- None today — clean session

### 🔜 Next session
- Mini project — complete robot arm simulator
- Combines everything from Days 2-6 into one clean notebook

---

## Day 5 — June 24, 2026
**Phase:** 1 — Foundations Sprint
**Time spent:** ~2 hrs

### ✅ What I did
- Watched CS223A Lecture 5 (Stanford Arm, Forward Kinematics)
- Implemented full 6DOF Stanford Arm using DH parameters
- Used prismatic joint (d3) for linear extension
- Chained all 6 transforms to get end effector pose
- Visualized Stanford Arm in 3D using matplotlib

### 🧠 What I learned
- Stanford Arm has prismatic joint 3 (d variable) + spherical wrist (joints 4,5,6)
- Spherical wrist joints share same origin point — simplifies position calculation
- Wrist lock singularity: when theta5=0, joints 4 and 6 align, lose 1 DOF
- enumerate() in Python gives both index and value in a loop
- f-strings embed variables directly inside print statements

### 🚧 Blockers & how I fixed them
- Jupyter kernel got stuck (In[*]) → fixed with Kernel → Restart & Run All

### 🔜 Next session
- CS223A Lecture 6 (Jacobian Matrix)
- Implement Jacobian for velocity kinematics
- Compute joint velocities from end effector velocities

---

## Day 4 — June 24, 2026
**Phase:** 1 — Foundations Sprint
**Time spent:** ~2 hrs

### ✅ What I did
- Watched CS223A Lecture 4 (DH Parameters, Forward Kinematics)
- Implemented DH transformation matrix from scratch in numpy
- Built full 3-joint robot arm forward kinematics using DH parameters
- Computed end effector position (x=0.720, y=1.626) from joint angles
- Visualized the robot arm using matplotlib — base, joints, end effector plotted

### 🧠 What I learned
- DH notation describes any robot joint with just 4 parameters (a, α, d, θ)
- For revolute joints θ is variable, for prismatic joints d is variable
- End effector position = chaining all joint transforms T1 @ T2 @ T3
- Translation (position) is always in column 3 of the 4x4 transform matrix
- plt.axis('equal') is critical for geometrically correct robot arm plots

### 🚧 Blockers & how I fixed them
- None today — clean session

### 🔜 Next session
- CS223A Lecture 5 (Jacobian, Velocity Kinematics)
- Implement Jacobian matrix for the 3-joint arm
- Compute joint velocities from end effector velocities

---

## Day 3 — June 21, 2026
**Phase:** 1 — Foundations Sprint
**Time spent:** ~2 hrs

### ✅ What I did
- Set up Obsidian Welcome page and Resources note
- Watched CS223A Lecture 3 (Forward Kinematics, Euler angles, Quaternions)
- Implemented Z-Y-X Euler angle rotation matrices in numpy
- Demonstrated gimbal lock singularity in code (cos(beta)=0)
- Implemented quaternion conversion from rotation matrix
- Proved quaternions handle singularities perfectly (magnitude=1.0 always)

### 🧠 What I learned
- Euler angles have 12 representations but all suffer from singularities
- Gimbal lock occurs when cos(beta)=0 — Z and X axes align, lose one DOF
- Quaternions use 4 parameters, always magnitude=1, never singular
- ROS2, Isaac Sim, Unity all use quaternions internally — now I know why

### 🚧 Blockers & how I fixed them
- IndentationError in Jupyter — caused by mixing tabs and spaces while typing
- SyntaxError: hyphen in variable name (pitch-singular) → fixed to pitch_singular

### 🔜 Next session
- CS223A Lecture 4 (Velocity kinematics, Jacobian)
- Start DH parameters implementation
- Begin forward kinematics for a 3-joint arm

---

## Day 2 — June 20, 2026
**Phase:** 1 — Foundations Sprint
**Time spent:** ~2 hrs

### ✅ What I did
- Watched CS223A Lecture 1 (course overview) and Lecture 2 (spatial descriptions)
- Implemented rotation matrices (Rz) in numpy
- Built a full 4x4 homogeneous transformation matrix from scratch
- Chained two transforms together simulating a 2-joint robot arm

### 🧠 What I learned
- Rotation matrix transforms a point between coordinate frames
- Homogeneous transforms combine rotation + translation into one 4x4 matrix
- Chaining transforms = how robot arms calculate end effector position
- Transform order matters — T1 @ T2 ≠ T2 @ T1

### 🚧 Blockers & how I fixed them
- Accidentally closed Jupyter browser tab — relaunched with same command

### 🔜 Next session
- CS223A Lecture 3 (forward kinematics)
- Implement Euler angles and quaternions in numpy

---

## Day 1 — June 19, 2026
**Phase:** 1 — Foundations Sprint
**Time spent:** ~3 hrs

### ✅ What I did
- Verified WSL2 Ubuntu 24.04 was installed and running correctly on Windows
- Confirmed GPU passthrough working — RTX 3050 Laptop + CUDA 13.0 accessible inside Linux
- Reset forgotten WSL Linux user password via PowerShell root login
- Updated Ubuntu system packages (168 packages upgraded, 126 security patches)
- Installed build essentials: gcc, g++, make, curl, wget, git, unzip
- Installed Miniconda and created dedicated `robotics` conda environment (Python 3.11)
- Installed full AI/CV/ML stack inside the environment:
  - PyTorch 2.12.1+cu130 (GPU-enabled)
  - OpenCV 4.13.0
  - NumPy 2.4.6
  - Pandas 3.0.3
  - Matplotlib 3.11.0
  - Scikit-learn 1.9.0
  - Jupyter
- Verified CUDA works from Python (`torch.cuda.is_available()` → True)
- Set up GitHub SSH authentication from inside WSL
- Created and structured `Robotics-AI-Journey` repo with 6-phase folder layout
- Wrote README documenting the full 12-month roadmap

### 🧠 What I learned
- WSL2 runs a full Linux kernel inside a `.vhdx` virtual disk on Windows — heavy disk I/O can corrupt it, fixed by `wsl --shutdown`
- `conda environments` keep project dependencies isolated from system Python — standard practice in ML/robotics
- SSH keys are the correct way to authenticate with GitHub (not passwords) — ed25519 is the modern standard
- CUDA 13.1 driver on Windows exposes correctly as CUDA 13.0 inside WSL2 — this is expected behavior
- Anaconda changed their Miniconda download URL from `/miniconda3/` to `/miniconda/` — always verify URLs from official docs
- `conda tos accept` is now required on fresh Miniconda installs before creating environments

### 🚧 Blockers & how I fixed them
- **Forgot WSL Linux password** → reset via `wsl -d Ubuntu-24.04 -u root` from PowerShell
- **Miniconda 404 error** → URL had changed, found correct one from official Anaconda docs
- **WSL2 disk I/O error mid-install** → ran `wsl --shutdown` from PowerShell, restarted, worked fine
- **GitHub clone failing** → GitHub username is `Arpeet-padhy` (with capital A and hyphen), not `arpeetpadhy`

### 🔜 Next session
- Set up Jupyter and run first notebook
- Start Phase 1: Stanford CS223A Lecture 1
- Begin MIT OCW 18.06 Linear Algebra

---

## Template for future entries — copy & fill this in each session

\`\`\`
## Day N — [Date]
**Phase:** [Phase number and name]
**Time spent:** X hrs

### ✅ What I did
-

### 🧠 What I learned
-

### 🚧 Blockers & how I fixed them
-

### 🔜 Next session
-
\`\`\`

---

*This log is part of my public robotics & AI self-study journey. Started June 2026.*
