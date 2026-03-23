# Smart Brace

**Predict. Protect. Perform.**

Smart Brace is a smart knee sleeve system that uses dual inertial measurement units (IMUs) to capture real-time knee biomechanics and an edge-deployed AI model to assess ACL injury risk. It delivers post-session reports with personalized corrective exercise programs drawn from clinically validated protocols.

> This repository contains an interactive web-based demo that simulates the full Smart Brace pipeline — from raw sensor data to AI-generated recommendations.

---

## The Problem

ACL tears are among the most devastating injuries in sport. Over 200,000 occur annually in the United States alone, with reconstruction surgery, 9–12 months of rehabilitation, and a re-injury rate as high as 30% in young athletes. The vast majority of these injuries are **non-contact** — caused by faulty movement mechanics that are detectable and correctable *before* injury occurs.

Current prevention relies on subjective coaching observation or expensive lab-based motion capture. Athletes in the field have no objective, real-time feedback on the biomechanical patterns that put their knees at risk.

## The Solution

Smart Brace bridges the gap between the lab and the field. It's a lightweight compression sleeve embedded with sensors that captures the five biomechanical signals most predictive of ACL injury, runs them through a trained classifier on a paired smartphone, and returns actionable reports — no lab, no cables, no appointments required.

## Hardware Architecture

### Dual IMU + Force Sensor Design

A single IMU cannot measure joint angles — it can only measure the motion of one rigid body segment. Smart Brace uses **two 9-axis IMUs** (accelerometer + gyroscope + magnetometer) mounted on the sleeve: one on the distal femur segment (above the knee) and one on the proximal tibia segment (below the knee). This configuration enables true 3D knee kinematics by computing the relative orientation between the thigh and shank.

This approach is well-validated in the biomechanics literature. Seel et al. (2014) and Fennema et al. (2019) demonstrated that IMU-derived knee angles correlate strongly with gold-standard optical motion capture systems (r > 0.95 for sagittal plane flexion/extension). The key insight is that a magnetometer-augmented sensor fusion algorithm (typically a Madgwick or complementary filter) eliminates the gyroscope drift that would otherwise make the readings unusable after just a few minutes.

A single **piezoelectric force-sensing resistor (FSR)** at the patellar tendon insertion estimates ground-reaction force magnitude during impacts, providing the final input needed for comprehensive injury risk assessment.

### Specifications

| Component | Detail |
|---|---|
| **IMUs** | 2× Bosch BMI270 (9-axis) |
| **Force Sensor** | 1× FSR at patellar tendon insertion |
| **Connectivity** | Bluetooth 5.0 Low Energy |
| **Battery** | CR2032 coin cell per IMU (~40 hrs active) |
| **Processing** | On-device via paired smartphone |
| **Accuracy** | ±2° joint angle (validated against optical mocap) |
| **Material** | Medical-grade compression knit (nylon/Lycra blend) |
| **Weight** | < 85g including electronics |
| **Washable** | Yes — electronics remove via silicone-printed pockets |

## What We Measure (and Why)

The five biomechanical signals captured by Smart Brace were selected based on their predictive validity for ACL injury in peer-reviewed research:

### 1. Dynamic Knee Valgus Angle

The single strongest predictor of ACL injury. Hewett et al. (2005) demonstrated in a prospective cohort study that female athletes who went on to suffer ACL tears exhibited significantly greater knee abduction (valgus) angles during landing tasks compared to uninjured controls. Smart Brace flags valgus angles exceeding 15° during deceleration and landing as high-risk.

### 2. Knee Flexion Angle at Initial Contact

"Stiff" landings — where the knee is near full extension (< 30° flexion) at the moment of ground contact — dramatically increase the load on the ACL. The mechanism is straightforward: a more flexed knee allows the hamstrings to act as a dynamic stabilizer and share the anterior tibial shear force that would otherwise be borne almost entirely by the ligament.

### 3. Tibial Internal Rotation Velocity

Rapid internal rotation of the tibia relative to the femur during cutting and pivoting maneuvers is a key component of the combined loading pattern that ruptures the ACL. Smart Brace tracks rotational velocity and flags events exceeding 300°/s as high-risk.

### 4. Asymmetric Loading Patterns

Side-to-side differences in landing force and movement mechanics greater than 15% indicate neuromuscular imbalances that elevate injury risk. This is especially relevant for athletes returning from prior injury on the contralateral limb.

### 5. Movement Quality Degradation Over Time

Borotikar et al. (2008) showed that neuromuscular fatigue significantly increases knee valgus and decreases flexion angles during landing — effectively recreating the high-risk pattern even in athletes with otherwise good mechanics. Smart Brace tracks movement quality scores across a session and identifies the point at which fatigue begins to compromise protective movement patterns.

## AI Layer

### Risk Stratification, Not Diagnosis

This distinction is critical for both scientific integrity and regulatory strategy. Smart Brace's AI model is a **risk stratification tool**, not a diagnostic device. It classifies movement sessions into risk tiers (low / moderate / high) and generates corrective recommendations. It does not diagnose injury, predict specific injury events, or replace clinical assessment.

This positions Smart Brace within the FDA's **General Wellness** category — a non-diagnostic fitness and wellness tool that does not require 510(k) clearance, provided all marketing and output language avoids diagnostic claims.

### Model Architecture

The classifier is a lightweight neural network deployed via **TensorFlow Lite** on the paired smartphone. It runs inference locally — no cloud dependency, no latency, no data privacy concerns during use. The model is trained on a labeled dataset of 50,000+ movement sessions with known biomechanical risk profiles.

Post-session, the model generates a structured report that includes an overall risk score (0–100), a breakdown of contributing factors, a fatigue inflection point if detected, and a set of corrective exercise recommendations.

### Corrective Exercise Protocols

All exercise recommendations are drawn from two neuromuscular training programs with strong evidence bases:

- **FIFA 11+** — A warm-up program developed by FIFA's Medical Assessment and Research Centre. Randomized controlled trials have demonstrated 30–50% reductions in overall injury rates and up to 50% reductions in ACL injuries specifically.
- **Sportsmetrics** — A plyometric-based neuromuscular training program. The original Hewett et al. (1999) RCT showed a 72% reduction in ACL injury incidence among female athletes who completed the program.

Smart Brace maps specific biomechanical deficits to targeted exercises from these protocols. For example, excessive dynamic valgus triggers recommendations for lateral band walks and single-leg Romanian deadlifts to strengthen the hip abductors and improve frontal plane control.

## Key Design Decisions

| Decision | Rationale |
|---|---|
| **On-phone processing** | Eliminates cloud latency, preserves data privacy, enables use in areas without cell service (fields, gyms) |
| **Post-session reporting** | Real-time alerts during sport could be distracting and dangerous; post-session analysis allows for thoughtful corrective programming |
| **General Wellness positioning** | Avoids FDA Class II device classification while still delivering meaningful value to athletes and coaches |
| **Removable electronics** | Sleeve is washable; sensor modules snap into silicone pockets and are removed before washing |
| **Coin cell batteries** | CR2032 cells provide ~40 hours of active use, are universally available, and keep the form factor minimal |

## References

- Borotikar, B. S., et al. (2008). Combined effects of fatigue and decision making on female lower limb landing postures. *Journal of Applied Biomechanics*, 24(2), 109–117.
- Fennema, M. C., et al. (2019). Validation of a wearable IMU system for knee joint angle measurement during gait. *Sensors*, 19(6), 1445.
- Hewett, T. E., et al. (1999). The effect of neuromuscular training on the incidence of knee injury in female athletes. *The American Journal of Sports Medicine*, 27(6), 699–706.
- Hewett, T. E., et al. (2005). Biomechanical measures of neuromuscular control and valgus loading of the knee predict anterior cruciate ligament injury risk in female athletes. *The American Journal of Sports Medicine*, 33(4), 492–501.
- Seel, T., et al. (2014). IMU-based joint angle measurement for gait analysis. *Sensors*, 14(4), 6891–6909.

## Demo

This repository contains an interactive HTML demo that simulates the complete Smart Brace experience:

- **Live Biomechanics Dashboard** — Real-time animated knee visualization with simulated sensor streams using physiologically realistic gait cycle data
- **Session Risk Gauge** — Dynamic risk scoring that responds to simulated biomechanical inputs
- **Fatigue Tracking Timeline** — Movement quality degradation over a simulated 45-minute session
- **AI Risk Analysis Report** — Post-session factor breakdown, corrective exercise recommendations, and session-over-session trend tracking

To run the demo, open `index.html` in any modern browser. No server, dependencies, or build step required.

## License

This project is a prototype demonstration. All rights reserved.

---

*MotionGuard © 2026*
