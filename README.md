Ansible Role: Cuda
==================

Installs NVIDIA CUDA, cuDNN, TensorRT (optionally) and TensorFlow.

Requirements
------------

Ubuntu 22.04 and Python 3.10.

Dependancies
------------

The jedimt.nvidia_repo role must be installed and run on the hosts to enable downloading packages for cudnn.

Role Variables
--------------

    ## Behavior control variables

    # Controls if the system is rebooted to complete install
    reboot_systems: false

    # Specify if packages should be downloaded from the Internet or
    # retrieved from a local HTTP/HTTPS link. If enabled, you must
    # provide alternate URLs for the CUDA and TensorRT packages.
    # NOTE: The package names must not be changed.

    use_local_sources: false
    local_cuda_pin_url: "http://linuxdev.tme.nebulon.com/install/cuda/cuda-ubuntu2204.pin"
    local_cuda_package_url: "http://linuxdev.tme.nebulon.com/install/cuda/cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb"
    local_tensorrt_package_url: "http://linuxdev.tme.nebulon.com/install/tensorrt/nv-tensorrt-local-repo-ubuntu2204-8.6.1-cuda-11.8_1.0-1_amd64.deb"

    ## Ubuntu distro and architecture - used in download URLs
    cuda_distro: "ubuntu2204"
    cuda_arch: "x86_64"

    ## CUDA variables
    # URL prefix for developer.download.nvidia.com
    # Helps shorten lines in playbook
    nvidia_url: "https://developer.download.nvidia.com/compute"

    # Semantic version string used in some URLs
    cuda_semantic_version: "11.8.0"

    # CUDA installer package version
    cuda_package_version: "520.61.05"

    # Base OS packages to install prior to NVIDIA components
    base_apt_packages:
      - python3-pip

    # CUDA packages
    cuda_apt_packages:
      - cuda-{{ cuda_version }}

    ## cuDNN variables
    cudnn_version: "8.9.6.50"
    cudnn_cuda_version: "cuda11.8"

    cudnn_apt_packages:
      - "libcudnn8={{ cudnn_version }}-1+{{ cudnn_cuda_version }}"
      - "libcudnn8-dev={{ cudnn_version }}-1+{{ cudnn_cuda_version }}"
      - "libcudnn8-samples={{ cudnn_version }}-1+{{ cudnn_cuda_version }}"
      - libfreeimage3
      - libfreeimage-dev

    ## TensorRT variables
    # Should TensorRT be installed?
    install_tensorrt: true

    # Version of TensorRT to install
    tensorrt_url_prefix: "https://developer.nvidia.com/downloads/compute/machine-learning/tensorrt/secure"
    tensorrt_version: "8.6.1"

    # APT packages for TensorRT
    tensorrt_apt_packages:
      - tensorrt

    ## Tensorflow
    # Python modules to install
    tensorflow_python_modules:
      - tensorflow[and-cuda]==2.13.0

    # Path to the Python virtual environment
    tensorflow_venv_folder: '{{ ansible_env.HOME }}/venv_tensorflow'

    # Python version for Python virtual environment
    venv_python_version: '3.10'


Example Playbook

    # ===========================================================================
    # NVIDIA Cuda installation
    # ===========================================================================
    - name: Install and configure NVIDIA CUDA components
      hosts: servers
      tags: play_cuda_install
      gather_facts: true

      vars_files:
        # Ansible vault with all required passwords
        - "../../credentials.yml"

      roles:
        - { role: jedimt.nvidia_container_toolkit }
        - { role: jedimt.cuda,
            skip_validation: true,
            use_local_sources: true,
            reboot_systems: true,
            cuda_semantic_version: "11.8.0",
            cuda_package_version: "520.61.05",
            cudnn_version: "8.9.6.50",
            cudnn_cuda_version: "cuda11.8",
            tensorrt_version: "8.6.1",
            venv_python_version: "3.10",
            tensorflow_python_modules: ["tensorflow[and-cuda]==2.13.0"]
        }


License
-------

MIT

Author Information
------------------

Aaron Patten
aaronpatten@gmail.com
