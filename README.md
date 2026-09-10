# 🚀 Automated AMD AI Stack: TheRock 7.15 / ROCm 10.0.0, PyTorch Stable, Transformers, Docker & vLLM

[![ROCm](https://img.shields.io/badge/ROCm-10.0.0-ff6b6b?logo=amd)](https://rocm.docs.amd.com/en/docs-10.0.0/about/release-notes.html)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.15%20%28Stable%29-ee4c2c?logo=pytorch)](https://pytorch.org/get-started/locally/)
[![Docker](https://img.shields.io/badge/Docker-29.8.x-blue?logo=docker)](https://www.docker.com/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04%20%7C%2026.04-e95420?logo=ubuntu)](https://ubuntu.com/download/server)
[![AMD Radeon AI PRO R9700](https://img.shields.io/badge/AMD-RDNA4%20Radeon(TM)%20AI%20PRO%20R9700-8B0000?logo=amd)](https://www.amd.com/en/products/graphics/workstations/radeon-ai-pro/ai-9000-series/amd-radeon-ai-pro-r9700.html)
[![AMD Ryzen AI](https://img.shields.io/badge/AMD-Ryzen%20AI%20-8B0000?logo=amd)](https://ryzen-ai.com/en/)
[![AMD CDNA MI300 Series](https://img.shields.io/badge/AMD-CDNA%20Instint(TM)%20Architecture-8B0000?logo=amd)](https://www.amd.com/en/technologies/cdna.html)

## 📌 Overview

This repository provides a fully automated, non-interactive deployment environment for AMD GPU software development targeting AI and HPC workloads on Ubuntu **24.04** and **26.04**. The setup is centered on AMD **TheRock 7.15** based on **ROCm 10.0.0** and the latest nightly PyTorch release.

At the platform layer, the script installs the AMD GPU kernel driver (`amdgpu`) **version 31.50.0** directly from AMD's official APT repository by automatically configuring the repository signing key and package source before installing the DKMS package. Using DKMS ensures the kernel driver is automatically rebuilt when the Linux kernel is updated, providing reliable compatibility across supported Ubuntu releases. The deployment then installs the **TheRock 7.15** runtime, including HIP support. The environment is designed to support RDNA4 GPUs. The deployment also configures the required system permissions (`video`, `render`, `sudo`) and installs kernel headers necessary for compiling GPU-accelerated native extensions.

For the AI framework layer, the script installs PyTorch 2.15 Nightly using ROCm 10.0.0 wheels from the official PyTorch ROCm nightly repository. This enables access to the latest HIP runtime capabilities, compiler optimizations, and kernel fusion features. The environment is complemented with widely used AI and data-processing libraries, including Transformers, Accelerate, Diffusers, Datasets, and SentencePiece, together with the required Python build tooling for immediate development, testing, benchmarking, and profiling of modern LLM, diffusion, and distributed workloads.

The developer toolchain further includes essential C/C++ build utilities and low-level GPU development packages such as `cmake`, `libstdc++` development headers, `git`, `git-lfs`, `libmsgpack`, and `TransferBench` for PCIe and HBM bandwidth validation. Runtime observability and diagnostics are supported through utilities including `htop`, `ncdu`, `rocminfo`, and `amd-smi`.

To validate the installation, the deployment automatically generates a verification script that performs end-to-end GPU checks, including ROCm runtime detection, PyTorch HIP availability, GPU enumeration, and successful on-device tensor execution.

The entire setup process is fully unattended and optimized for both workstation and server deployments. Before installation, the script detects existing ROCm or pip-installed PyTorch environments and removes conflicting packages - including ROCm-specific PyTorch builds - before configuring the AMD package repository and installing the kernel driver, ensuring a clean, reproducible deployment state.

---

## 🖥️ Supported Platforms

| **Component**      | **Supported Versions**                                |
|---------------------|------------------------------------------------------|
| **OS**            | Ubuntu 24.04.x (Noble Numbat), Ubuntu 26.04 (Resolute Raccoon) |
| **Kernels** tested       | 6.8.0-139 (24.04.4) • 7.0.0-28 (26.04)                      |
| **GPUs**          | AMD **RDNA4** • **RDNA3** • **CDNA4** • **CDNA3** • **CDNA2** • **CDNA1**           |
| **APUs**        | AMD Ryzen™ AI 300 and 400 series                                    |
| **TheRock/ROCm**          | 7.15 / 10.0.0                                                |
| **PyTorch**       | torch 2.15.0.dev20260907+rocm10.0, torchvision 0.30.0.dev20260908+rocm10.0       |       |

**⚠️ Note**: **Ubuntu 20.04.x (Focal Fossa)** and **Ubuntu 22.04.x (Jammy Jellyfish)** is **not supported**. The last compatible ROCm version for 20.04 is **6.4.0** and **7.4.2** for 22.04.

---

## ⚡ Features
- Automated **TheRock GPU drivers + HIP SDK** installation
- **PyTorch Stable** with GPU acceleration
- Preinstalled **Transformers**, **Accelerate**, **Diffusers**, and **Datasets**
- Integrated **Docker environment** with ROCm GPU passthrough
- **vLLM Docker images** for **RDNA4** and **CDNA**
- Optimized for **AI workloads**, **LLM inference**, and **model fine-tuning**

---

## 🚀 Installation

### 1️⃣ **System preperation**
Install **Ubuntu 24.04.4 LTS** or **Ubuntu 26.04 LTS** (Server or Desktop version).

**⚠️ Note**: This Guide uses Ubuntu **24.04 LTS**

**Recommendations:**
- Use a fresh Ubuntu installation if possible
- Assign the full storage capacity during installation
- Install **OpenSSH** for remote SSH management
- The script automatically checks the system for installed versions of ROCm/TheRock, PyTorch, and Docker, and removes them if found
  - On a fresh Ubuntu installation, the script automatically skips the deinstallation routine, as illustrated below
    <img width="1555" height="334" alt="image" src="https://github.com/user-attachments/assets/bb2e0c09-af3f-490e-a388-4da4e62d74f2" />
  - If an existing version is detected, it will be deleted, regardless of whether it is the same or an older release.
    <img width="1739" height="607" alt="image" src="https://github.com/user-attachments/assets/7d618bd5-c910-4118-b1b4-a332af4ee152" />

- SBIOS settings:
  - When using Linux, you should disable Secure Boot
  - On WRX80 and WRX90 motherboard solutions, make sure SR-IOV is enabled — there are known issues with Ubuntu Linux detecting the network

### 2️⃣ **Download the Script from the Repository**
```bash
wget https://raw.githubusercontent.com/JoergR75/amd-therock-7.15-rocm-10-0-0-pytorch-docker-cdna-rdna-automated-deployment/refs/heads/main/script_module_TheRock_7.15_ROCm_10.0.0_Ubuntu_24.04-26.04_pytorch.sh
```

<img width="2190" height="550" alt="image" src="https://github.com/user-attachments/assets/9cd592dd-6602-4edf-b490-d06954d47fe5" />

### 3️⃣ **Run the Installer**
```bash
bash script_module_TheRock_7.15_ROCm_10.0.0_Ubuntu_24.04-26.04_pytorch.sh
```
**⚠️ Note**: Entering the user password may be required.

<img width="2177" height="473" alt="image" src="https://github.com/user-attachments/assets/9fe5ac6f-d9b1-4788-ba6e-92af7de97cc2" />

The installation takes ~15 minutes depending on internet speed and hardware performance.

### 4️⃣ **Reboot the System**
After the successful installation, press "y" to reboot the system and activate all installed components.

<img width="2199" height="636" alt="image" src="https://github.com/user-attachments/assets/0eed9638-8a5e-486c-a04d-1deaa07094f5" />

## 🧪 Testing TheRock/ROCm + PyTorch Version and GPU Hardware Setup

After rebooting the system, run the diagnostic script to verify that the ROCm software stack, PyTorch installation, and AMD GPU hardware are correctly detected and functioning.

The script creates and runs a simple Python diagnostic file, `test.py`, to confirm that PyTorch with ROCm support is installed correctly and that GPU compute is working end-to-end.

### What the script verifies

- ✅ Detects the operating system version and Linux kernel version.
- ✅ Reports the installed CPU model and total system memory.
- ✅ Verifies the installed AI software stack, including:
  - PyTorch version
  - TheRock/HIP runtime version
  - ROCm version
  - AMDGPU driver version
  - Transformers version
  - AMD SMI tool and library versions
- ✅ Confirms that ROCm is available to PyTorch using `torch.cuda.is_available()`.
- ✅ Automatically detects all installed AMD GPUs and reports key hardware details:
  - GPU model
  - Free and total VRAM
  - PCIe device address
  - PCIe link width
  - PCIe link speed
- ✅ Verifies that each GPU is operating at the expected PCIe bandwidth, for example PCIe Gen5 x16.
- ✅ Performs a PyTorch tensor operation on every detected GPU.
- ✅ Confirms successful GPU initialization, memory allocation, and compute execution for each GPU.
- ✅ Provides a quick system health check to confirm that the workstation is ready for AI inference and training workloads.

Example usage:
```bash
python3 test.py
```
Expected Output Example:

| Ubuntu 26.04 LTS | Ubuntu 24.04.4 LTS |
|--------|--------|
| ![](https://github.com/user-attachments/assets/d7731106-ea40-4f93-9509-680c684973b8) | ![](https://github.com/user-attachments/assets/82f7bc98-6693-4f4f-8009-4e46a8b85e7b) |


With `amd-smi`, you can verify all available GPUs (in this case, 2x Radeon AI PRO R9700 GPUs).

<img width="2058" height="752" alt="image" src="https://github.com/user-attachments/assets/bd96164d-f9c3-49e9-a669-275fd4b0ba88" />

⚠️ **Caution:**  
Make sure **"Re-Size BAR"** is enabled in the **SBIOS**.  
If it is disabled, **P2P** will be deactivated.

## 📶 TransferBench

**TransferBench** is a utility for benchmarking simultaneous memory transfers between user-specified devices (CPUs, GPUs, and NICs).

### What it does

`TransferBench` is a diagnostic tool that measures **memory bandwidth performance** between:

- ✅ Host (CPU) ↔ GPU(s)  
- ✅ GPU ↔ GPU (if multiple GPUs are installed)  
- ✅ GPU internal memory

### Features
TransferBench supports the following features:
- ✅ Multiple Executors: CPU threads, GPU compute kernels (GFX), GPU DMA engines (SDMA), and NIC RDMA.
- ✅ Multi-input or multi-output (MIMO) transfers: Element-wise sum from multiple SRCs to multiple DSTs.
- ✅ Multinode execution: Runs across distributed systems using MPI or sockets.
- ✅ Flexible configuration: Configure benchmarks using config files or presets.
- ✅ Flexible hardware: Runs HIP and CUDA programs on both AMD and NVIDIA hardware.

### How to install?
1️⃣ Install prerequisites
```bash
sudo apt install -y amdrocm-core-sdk10.0
sudo apt install -y rdma-core libibverbs-dev ibverbs-utils
sudo apt install -y openmpi-bin libopenmpi-dev
sudo apt-get install -y mpich libmpich-dev
```

2️⃣ Compile from Source
```bash
git clone https://github.com/ROCm/TransferBench.git
cd TransferBench
mkdir build && cd build
cmake ..
make
```
Compilation can take several minutes depending on HW used

<img width="2277" height="1712" alt="image" src="https://github.com/user-attachments/assets/99e735dc-78c0-4cb4-8427-46670e7a29ab" />

3️⃣ Get P2P transfer and bandwidth tested
```bash
./TransferBench p2p
```
The results (256MB transfer) indicate that GPU-to-GPU P2P communication is working correctly and that both GPUs exhibit nearly identical performance.
<img width="1725" height="828" alt="image" src="https://github.com/user-attachments/assets/30b9e4c4-9e45-4629-abd6-82ba9a04910a" />

### ⚙️ How to Enable **Re-Size BAR** in SBIOS (example ASRock WRX90 evo)

1. Enter **SBIOS**

<img width="1007" height="760" alt="{F9649127-0F1F-4E14-8008-1F3782FBBDEF}" src="https://github.com/user-attachments/assets/9685c1a4-ecab-4fea-8e91-dd21b9869c7e" />

3. Navigate to **Advanced**

<img width="1018" height="761" alt="{135D3B4C-0732-4652-A3C0-1224D275A515}" src="https://github.com/user-attachments/assets/b1cdc3ce-b526-4cdc-b44f-71d1119cf6d7" />

5. Go to **PCI Subsystem Settings** and change **Re-Size BAR Support** to **Enable** 

<img width="1016" height="761" alt="{3C54C3DA-8B82-483C-AEA5-D0A511508780}" src="https://github.com/user-attachments/assets/60536e2b-e59f-4486-a1fc-ab3ff33a3cd8" />

## 🐋 Docker Integration

The script sets up a Docker environment with GPU passthrough support via ROCm.

Check Docker installation and version
```bash
docker -v
```

<img width="2018" height="101" alt="image" src="https://github.com/user-attachments/assets/cca4209b-8543-4787-88c9-bef6b1e94db5" />

### 🤖 vLLM Docker Images

To use vLLM optimized for RDNA4 and CDNA:

Use the container image you need.

**RDNA4** architecture running on Ubuntu 24.04
```bash
docker pull rocm/vllm-dev:nightly_rocm10_20260903
```

<img width="2164" height="905" alt="image" src="https://github.com/user-attachments/assets/98c68113-56e1-4978-9aba-6af23c67609d" />

Further vLLM Docker versions for RDNA4 can be verified on Docker Hub:  
https://hub.docker.com/r/rocm/vllm-dev/tags?name=navi or https://hub.docker.com/r/vllm/vllm-openai-rocm/tags

or for **CDNA** architecture
```bash
sudo docker pull rocm/vllm:latest
```

Run vLLM with all available AMD GPU access (example for RDNA4 on Ubuntu 24.04)
```bash
sudo docker run -it \
    --device=/dev/kfd \
    --device=/dev/dri \
    --security-opt seccomp=unconfined \
    --group-add video \
    --entrypoint /bin/bash \
    rocm/vllm:rocm7.14.0_rdna_ubuntu24.04_py3.14_pytorch_2.11.0_vllm_0.23.0
```

<img width="2068" height="284" alt="image" src="https://github.com/user-attachments/assets/b84604af-ef10-4b4e-98a2-55c661ff8a99" />

With `amd-smi`, you can verify all available GPUs (in this case, 2× Radeon AI PRO R9700 GPUs).

<img width="2143" height="747" alt="image" src="https://github.com/user-attachments/assets/c36e81a2-db8a-41e0-8a63-8d3031ad7d19" />

If you need to add a specific GPU, you can use the **passthrough** option.  
First, verify the available GPUs in the `/dev/dri` directory (host).
```bash
cd /dev/dri && ls
```

<img width="1981" height="97" alt="image" src="https://github.com/user-attachments/assets/0268488e-4969-49ab-94af-4f2f24a6555f" />

Let's choose **GPU2**, also referred to as **"card2"** or **"renderD129"**.
```bash
sudo docker run -it \
    --device=/dev/kfd \
    --device=/dev/dri/card2 \
    --device=/dev/dri/renderD129 \
    --security-opt seccomp=unconfined \
    --group-add video \
    --entrypoint /bin/bash \
    rocm/vllm:rocm7.14.0_rdna_ubuntu24.04_py3.14_pytorch_2.11.0_vllm_0.23.0
```
GPU2 has been added to the container

<img width="2176" height="904" alt="image" src="https://github.com/user-attachments/assets/3480986c-97bc-4c1c-b949-cd0f708985d2" />

## How to Save a Modified Docker Container

1️⃣ Open your container and modify it as needed (e.g., install packages, change configurations).

**⚠️ Note: Do not stop or close the container!**

2️⃣ Open another terminal (CLI) window.

3️⃣ Verify the running and stopped containers:
```bash
sudo docker ps -a
```

<img width="844" height="126" alt="image" src="https://github.com/user-attachments/assets/b879c0a2-a071-4307-adba-0da66534fd15" />

4️⃣ In this example, we want to save the running container `loving_wescoff` as a new image named `rocm/vllm-dev:rocm7.2.1_navi_ubuntu24.04_py3.12_pytorch_2.9_vllm_0.16.0_2`:
```bash
docker commit loving_wescoff vllm/vllm-openai-rocm:v0.20.1_2
```

<img width="842" height="46" alt="image" src="https://github.com/user-attachments/assets/968c0c38-20c9-4cac-8928-c4a7797e15a7" />

5️⃣ Verify that the new image was created successfully:
```bash
sudo docker images
```

<img width="855" height="138" alt="image" src="https://github.com/user-attachments/assets/86a03be1-e4e2-4e88-8a28-6d362fb14d7b" />

6️⃣ Start the new container with one GPU (renderD129):
```bash
sudo docker run -it \
    --device=/dev/kfd \
    --device=/dev/dri/card2 \
    --device=/dev/dri/renderD129 \
    --security-opt seccomp=unconfined \
    --group-add video \
    vllm/vllm-openai-rocm:v0.20.1_2
```

<img width="828" height="395" alt="image" src="https://github.com/user-attachments/assets/e7349f84-b08b-4500-988d-19aff77025be" />
