# main-goal
Bu sayfada, projenin tamamının ana hedefi yer almaktadır.


```mermaid
%%{init: {'theme':'base', 'themeVariables': {
  'primaryColor': '#5a1f0f',
  'primaryBorderColor': '#fb923c',
  'primaryTextColor': '#ffedd5',
  'lineColor': '#fb923c',
  'tertiaryColor': '#fb923c',
  'clusterBkg': '#0b1020',
  'clusterBorderColor': '#f97316',
  'fontFamily': 'Inter, ui-sans-serif, system-ui'
}}}%%

flowchart TB

  %% Bare Metal Host (2 VM içerir)
  subgraph HOST["Bare Metal Host"]
    direction LR

    %% Sidecar VM
    subgraph VM1["Sidecar VM"]
      ANS["Ansible Controller"]
    end

    %% Vagrant ile oluşturulmuş Windows VM
    subgraph VM2["Windows VM (Vagrant)"]
      direction TB
      WIN["Multiboot Windows 11 VM"]
      DEB["Multiboot Debian VM (25GB)"]
      MAC["Multiboot macOS VM"]
      OMV(("OMV-share (20GB • D:)"))
    end
  end

  %% İlişkiler
  ANS -->|Provision / Yönetim| VM2

  %% Çift yönlü bağlantılar
  WIN <-->|SMB| OMV
  DEB <-->|ZFS| OMV
  MAC <-->|SMB| OMV


```
---
## Hiyerarch

  The main goal of our project is to enable users to access their files from anywhere, with complete freedom and flexibility. Files will be stored distributed throughout your own repositories, allowing you to have full access to them wherever you are. The topology seen above is the lab environment created for the first phase. This lab environment involves a topology where the OMV disk, located on a multi-boot computer, is automatically mounted regardless of the operating system it's running on, and automatically mounted to the operating system's home folder using a suitable file sharing protocol. This allows you to easily access your OMV-shared folder from anywhere, regardless of your location. The entire structure in the task is managed by sidecar ansible. You can view the task's process diagram below:




---



```mermaid

timeline
    title Project Roadmap
    section Başlangıç
      14 Ağustos : 🎯 Proje başlangıç
    
    section Lab Ortamı Oluşturma
      21 Ağustos : Task1
      29 Ağustos : -

    section Eylül Görevleri
      5 Eylül    : -
    

    section Ekim Görevleri
      3 Ekim     : -
  

    section Kasım Görevleri
      7 Kasım    : -
   
Windows 11
```
