# 📓 Dev Log — Robotics & AI Journey

A daily log of what I built, learned, and struggled with.
Newest entries at the top.

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
