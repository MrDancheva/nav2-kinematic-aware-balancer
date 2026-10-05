# 🤖 STABLE-Nav2
### Self-Balancing Robot Alternative Route Manager for ROS 2 / Nav2

[![ROS 2](https://img.shields.io/badge/ROS%202-Iron%20%7C%20Jazzy-blue?logo=ros)](https://docs.ros.org/)
[![Nav2](https://img.shields.io/badge/Navigation2-Enabled-green?logo=ros)](https://docs.nav2.org/)
[![C++](https://img.shields.io/badge/C%2B%2B-17-orange?logo=c%2B%2B)](https://isocpp.org/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

> 🎯 **Amaç / Goal:**  
> Denge robotları (inverted pendulum) için Nav2 üzerinde çalışan, kinematik kısıtları ve sarsıntı (jerk) minimizasyonunu gözeten **çok kriterli alternatif rota üretimi ve adaptif seçim sistemi.**  
> A multi-criteria alternative route generation and adaptive selection framework on Nav2, specifically designed for self-balancing robots with strict kinematic constraints and jerk minimization.

---

## 📌 İçindekiler / Table of Contents
- [Proje Özeti](#-proje-özeti)
- [Neden Bu Proje?](#-neden-bu-proje)
- [Sistem Mimarisi](#-sistem-mimarisi)
- [MCDM Puanlama Modeli](#-mcdm-puanlama-modeli)
- [Kurulum ve Kullanım](#-kurulum-ve-kullanım)
- [Proje Durumu](#-proje-durumu)
- [Ekip ve İletişim](#-ekip-ve-iletişim)

---

## 🎯 Proje Özeti
Bu proje, **ROS 2** ve **Navigation2 (Nav2)** ekosistemi üzerinde çalışan **denge robotları (self-balancing / inverted pendulum)** için geliştirilmiş, modüler bir alternatif rota yönetim sistemidir.

Standart Nav2 planlayıcıları, mesafeyi minimize etmeye odaklanır ve ani duruşlar veya keskin dönüşler içerebilir. Bir denge robotu için bu durum **devrilmeye** yol açar. Bu sistem:
1. 🛤️ Aynı başlangıç ve hedef arasında **N adet anlamlı alternatif rota** üretir.
2. 📊 Bu rotaları; *sarsıntı (jerk), kinematik uygunluk, dinamik risk ve mesafe* gibi kriterlerle puanlar (MCDM).
3. 🔄 Ortam değiştiğinde sıfırdan hesaplama yapmak yerine, **mevcut alternatifler arasından en güvenli olanına proaktif geçiş** yapar.

---

## ❓ Neden Bu Proje?

| Standart Nav2 Yaklaşımı | STABLE-Nav2 Yaklaşımı |
| :--- | :--- |
| Tek bir "en kısa" rota üretir | N adet alternatif rota üretir ve saklar |
| Ani duruş/keskin dönüş içerebilir | Jerk-minimize edilmiş, pürüzsüz yollar üretir |
| Engel gördüğünde sıfırdan replan yapar | Mevcut alternatifler arasından anında geçiş yapar |
| Denge robotu devrilebilir | Kinematik kısıtlarla denge korunur |

---

## 🏗️ Sistem Mimarisi

```mermaid
flowchart LR
    A[Nav2 Planner Server<br/>Smac Planner] -->|nav_msgs/Path| B[Candidate Route<br/>Generator]
    C[Costmap2D] -->|Grid Map| B
    B -->|N adet Path| D[Feature<br/>Extractor]
    D -->|Metrics| E[Multi-Criteria<br/>Scorer<br/>MCDM]
    F[Adaptive Weight<br/>Manager] -->|w_i weights| E
    E -->|Scored Paths| G[Route Selector]
    G -->|selected_path| H[Nav2 Controller<br/>MPPI / DWB]
    H --> I[🤖 Denge Robotu]
    
    J[Costmap Update] -.->|re-score| E
