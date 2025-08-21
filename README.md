# main-goal
Bu sayfada, projenin tamamının ana hedefi yer almaktadır.


```mermaid
%%{init: {'theme':'base', 'themeVariables': {
  'primaryColor': '#3b82f6',
  'primaryBorderColor': '#1e40af',
  'primaryTextColor': '#eff6ff',
  'lineColor': '#60a5fa',
  'tertiaryColor': '#1e3a8a'
}}}%%

flowchart TB

  %% Bare Metal Host
  subgraph HOST["🖥️ Bare Metal Host"]
    direction LR

    %% Sidecar VM
    subgraph VM1["⚙️ Sidecar VM"]
      ANS["Ansible Controller"]
    end

    %% Windows VM (Vagrant ile oluşturulmuş)
    subgraph VM2["🪟 Windows VM (Vagrant)"]
      direction TB
      WIN["🪟 Windows 11"]
      DEB["🐧 Debian (25GB)"]
      OMV["📦 OMV-share (20GB - D:)"]
    end
  end

  %% İlişkiler
  ANS -->|Provision / Yönetim| VM2
  WIN --- OMV
  DEB --- OMV
```
