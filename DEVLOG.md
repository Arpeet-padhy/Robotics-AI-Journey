# 📓 Dev Log — Robotics & AI Journey

A daily log of what I built, learned, and struggled with.
Newest entries at the top.

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
