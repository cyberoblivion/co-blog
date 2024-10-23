---
layout: post
title:  "Running Windows 11 on a Linux KVM"
date:   2024-10-22 20:34:44 -0400
categories: kvm
bootstrap-enabled: false
author: "Ben Erridge"
---

<strong>Back to the hacks!</strong>

<!-- <h1>Mastering the Arcane: Running Windows 11 on a Fedora 40 KVM</h1> -->

<p>In this guide, we'll walk through configuring a KVM (Kernel-based Virtual Machine) on a Fedora host to run a Windows 11 guest instance. This setup ensures that Windows continues to boot and function properly, even after installation and multiple reboots.</p>

<p>As a developer, I prefer using Fedora Linux as my primary desktop and development environment. However, there are times when I need to run Windows—whether it's to test software compatibility on the Edge browser, try out a Windows-only application, or use niche tools like the Bose firmware upgrader that require a Windows operating system. In these cases, running a Windows virtual machine (VM) is far more convenient than dual-booting, as it allows me to switch between Linux and Windows seamlessly without restarting my PC.</p>
<p>One of the key challenges when running Windows 11 in a virtual machine on Linux is configuring the firmware and TPM module to ensure compatibility with Windows 11 requirements.</p>

<h2>Why a TPM is Important for Running Windows 11</h2>

<p>Windows 11 has specific requirements that include the presence of a Trusted Platform Module (TPM) version 2.0. While it is possible to install Windows 11 without a TPM or with a misconfigured TPM, you may encounter issues after a few reboots, such as the system booting into recovery mode. The TPM provides key security features, including hardware-based key storage, secure boot, and protection against firmware-level attacks.</p>

<p>If you're running Windows 11 in a virtual machine, it's highly recommended to configure a virtual TPM (vTPM). This emulates the TPM functionality in a virtual environment, ensuring your VM benefits from the same security features as a physical Windows machine. Disabling or bypassing the TPM requirement is technically possible but not advisable due to the increased vulnerability to attacks.</p>

<h2>Why a Virtual TPM (vTPM) Matters</h2>

<p>Without a TPM (or vTPM), VMs are more vulnerable, especially if the hypervisor or host system is compromised. They lack the hardware-based security features that a physical TPM provides, such as encrypted key storage, secure boot validation, and enhanced protection against firmware-level threats.</p>

<p>On the other hand, configuring a vTPM in your VM setup allows you to achieve similar security levels as physical systems. It ensures your Windows 11 guest instance can securely manage encryption keys, validate the boot process, and protect sensitive data, even within a virtualized environment.</p>

<h2>Summary</h2>

<p>By configuring your KVM environment with a vTPM on Fedora, you can run Windows 11 securely and without the common boot issues that arise when TPM requirements aren't met. This setup allows you to test Windows software or work with Windows-only tools while still having access to your Linux development environment.</p>

<br/>


 <h3>Instructions</h3>
 <ol>
    <li>
      Ensure virtualization is supported
      {% highlight shell %}
if grep -E 'vmx|svm' /proc/cpuinfo; then
  echo -e "\e[32mOK\e[0m"  # Green color for OK
else
  echo -e "\e[31mUnsupported\e[0m"  # Red color for Unsupported
fi
      {% endhighlight %}
    </li>
    <li>
      Ensure virtualization is installed and running
      {% highlight shell %}
sudo dnf group install --with-optional virtualization;
sudo systemctl start libvirtd;
sudo systemctl enable libvirtd;
      {% endhighlight %}
    </li>
    <li>
      The simplest way to complete this task is to use virt-manager to create a new VM through the graphical user interface. Follow these 
      steps. 
        <div class="info-panel">
          <div class="info-icon">&#8505;</div>
          <div class="info-content">
            <strong>Note:</strong> Cockpit Virtual Machines does not appear to support changing the firmware of a VM.
          </div>
        </div>      
      start virt-manager 
      {% highlight shell %}
sudo virt-manager gui
      {% endhighlight %}
      Create new VM select File → New Virtual Machine.<br/>
      <img src="/assets/win11kvm/new-vm-step-0.png" width="250px"/>
    </li>
    <li>
      Select installation source. I will use a downloaded ISO image. 
      <div class="info-panel">
          <div class="info-icon">&#8505;</div>
          <div class="info-content">
            <strong>Note:</strong> Windows ISO's are available from the Windows website here           
         <a href="https://www.microsoft.com/en-us/software-download/windows11">
         <br/>
         <img src="https://www.microsoft.com/favicon.ico?v2" height="25px"><span> Download Windows 11</span>
         </a>
          </div>
      </div>  
      <img src="/assets/win11kvm/new-vm-step-1.png" width="250px"/>
    </li>
    <li>
      Choose install media and choose operating system. Choose the ISO file for Windows 11 as your install media, then uncheck "Automatically detect"  and set the OS variant as 'Windows 11' in the dropdown.
      <br/>
      <img src="/assets/win11kvm/new-vm-step-2.png" width="250px"/>
    </li>
       <li>
      Choose the Memory and CPU parameters of your preference. For a smooth Windows 11 experience, allocate at least 8 GB of RAM and 
4 CPU cores. Adjust based on your system’s resources.
      <br/>
      <img src="/assets/win11kvm/new-vm-step-3.png" width="250px"/>
    </li>    
    <li>
      Choose storage configuration
      <br/>
      <img src="/assets/win11kvm/new-vm-step-4.png" width="250px"/>
    </li>
    <li>
      <strong>Important:</strong> Before proceeding, ensure you check the 'Customize configuration before install' box. This allows you to adjust the chipset, 
firmware, and add a vTPM, which are required for Windows 11
      <br/>
      <img src="/assets/win11kvm/new-vm-step-5.png" width="250px"/>
    </li>
    <li>
      <strong>Important:</strong> In the 'Overview' section, set the firmware to  OVMF_CODE.secboot.fd  for UEFI and Secure Boot support. Under 'Chipset,' 
      choose  i440FX  for compatibility with Secure Boot features
      <br/>
      Chipset: <strong>i440FX</strong>
      <br/>
      Firmware: <strong>OVMF_CODE.secboot.fd</strong>
      <br/>
      <img src="/assets/win11kvm/new-vm-change-firmware.png" width="250px"/>
    </li>
    <li>
      <strong>Important:</strong> Important: Ensure vTPM is configured for Windows 11 compatibility.
      Update the firmware version, and add a vTPM, which are required for Windows 11
      <br/>
      Model: <strong>TIS</strong>
      <br/>
      version: <strong>2.0</strong>
      <br/>
      <img src="/assets/win11kvm/new-vm-customize-tpm.png" width="250px"/>
    </li>
    <li>
      Once the configuration is complete, select 'Begin Installation.' After installation, ensure you install any necessary drivers or tools such as 
the virtio drivers for improved VM performance. The latest Windows VirtIO binary drivers are available here curtesy of Fedora!: 
      <br/>
      <a href="https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/virtio-win-guest-tools.exe">
        Download VirtIO binary drivers
      </a>
    </li>
 </ol>
  
 
<div class="info-panel">
  <div class="info-icon">&#8505;</div>
      <div class="info-content">
      <strong>Note:</strong>
      Alternatively from the command line run this command. 
      <br/>you must update the windows ISO location and network configuration to match your environment
{% highlight shell %}
sudo virt-install \
  --name win11-tpmv2 \
  --memory 8192 \
  --vcpus 4 \
  --cdrom /path/to/windows11.iso \
  --disk size=50G,format=qcow2 \
  --os-variant win11 \
  --boot uefi,loader=/usr/share/edk2/ovmf/OVMF_CODE.secboot.fd,nvram_template=/usr/share/edk2/ovmf/OVMF_VARS.fd \
  --features smm=on \
  --network bridge=virbr0,model=virtio \
  --graphics spice \
  --video qxl \
  --tpm backend.type=emulator,backend.version=2.0,model=tpm-tis 
{% endhighlight %}
  </div>
</div>  


<br/>
<p align="center">
  <img src="/assets/logo_final.svg" height="250px"/>
</p>
<div class="citation">
    <p>
        Source: <a href="https://getlabsdone.com/how-to-install-windows-11-on-kvm/" target="_blank">Windows 11 on KVM – How to Install Step by Step?</a> by Author Saifudheen Sidheeq, published on GetLabsDone.
    </p>
</div>

