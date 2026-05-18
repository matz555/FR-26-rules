# AiR Drone for FIRA Malaysia Cup
## Autonomous Lock & Drop Drone Competition

**Document Version:** Rev. 1.0  
**Date:** February 2026

---

## Artificial Intelligent Related Drone (AiR Drone) Competition Rules
### (Autonomous Drone Lock and Drop)

**Authors:**

- **NOOR FAIZAH BINTI ZOHARDIN** — Politeknik Port Dickson — noorfaizah@pmm.edu.my
- **WONG WEI MING** — Politeknik Port Dickson — wongweiming5351@gmail.com

---

## 1.0 General Information

**1.1** Team members — Each team consists of 2 Students and 1 Supervisor.

**1.2** The team's drone may use any stability or avoidance sensors to improve the research on Drone stability. The mission should be completed without relying on GPS/GNSS data.

**1.3** The participant should be around the competition field or pit stop during the competition.

**1.4** If the team is late for 10 minutes after the referee call, the team will be considered a walkover for that attempt.

---

## 2.0 During Game

**2.1** The referee can stop the game anytime if the drone damages the field or injures someone.

**2.2** Participants cannot fly or hover the drone without the referee's permission.

**2.3** Participants cannot enter the competition field without the referee's permission.

**2.4** Once the drone crashes, touches the ground multiple times, lands on the ground, or tangles on the net, the attempt is considered to end, and the score will be counted before the drone touches the target object.

---

## 3.0 Rules Of The Game

**3.1** These rules aim to develop a new autonomous drone system using computer vision and machine learning algorithms for indoor environments without relying on GPS/GNSS data.

**3.2** The physical specification of the drones must abide by the rules below. Otherwise, the drone is not allowed to take part in the competition.

- The drone's size (including propellers) should be smaller than **100 cm**.
- The drone's weight (when flying) should be less than **3.5 kg**.
- Only electric motors and actuators are allowed. Using fuel-based engines is **not permitted**.

**3.3** There is no limitation on the type of drone (airship, helicopter, etc.) except that the drone should be capable of vertical flight.

**3.4** Based on the competition venue, it is possible to have a level of magnetic or electromagnetic interference in the playing field. Although the OCs will try their best to prepare the field with the best possible condition, it is recommended to have a complementary sensor system for compass reading (heading) and low dependency on wireless connections (wifi).

**3.5** All the decisions made by the game officials are final.

**3.6** Referees have the right to rule out any attempt if any suspicious activity or an unfair attempt is found and also to stop any dangerous run.

---

## 4.0 Game Structure

The drone must perform the following steps in order:

**4.1 Autonomous Take-off:** The drone takes off from the designated take-off zone.

**4.2 Target Identification:** The drone must autonomously detect and lock onto the correct target placed on the platform.

**4.3 Target Hit (Physical Contact):** The drone must make physical contact with the target using its body or an attached non-damaging mechanism. The hit must be intentional and controlled.

**4.4** Upon target initial contact with the ground, the official race time shall be recorded. The drone is then allocated a maximum window of **15 seconds** to successfully complete its landing sequence.

**4.5** After hitting the target, the drone which lands safely in the designated landing zone will be given bonus score.

**4.6** The drone must remain airborne during the hit (no landing on the platform to hit) and maintain flight stability after contact.

**4.7** Contact mechanism must be **non-sharp, non-destructive, and safe**. Any intentional damaging design is prohibited. The drone must not knock down the platform, damage the target structure, or endanger people or equipment.

**4.8** Each team will be given **3 attempts**; the highest score among the 3 attempts will be recorded. (These rules may vary and change depending on the number of participants that participate.)

**4.9** For this mission, teams will be given a time limit of **5 minutes** to complete it. (These rules may vary and change depending on the number of participants that participate.)

---

## 5.0 Arena, Platform & Target Specifications

**5.1 Platform Height:** 1 meter from ground level.

**5.2 Target Height:** 1 foot (≈30 cm) above the platform surface.

**5.3 Distance:** Distance from starting point to platform = **1 meter**.

**5.4 Target Arrangement:** Targets will be placed on top of the platform.

**5.5** Target position for each team will be assigned by random draw (*undian*).

**5.6** Teams are not allowed to adjust target position or platform setup.

### Figure 1: Platform and Target Object

**Image description:** A 3D isometric illustration of the competition setup. The platform is depicted as an inverted U-shaped (gate-like) wooden structure with two vertical legs resting on orange stabilizer feet. The platform's top surface is colored yellow and is labeled **150 cm Width**. The vertical height of the structure is labeled **100 cm Height**. Four orange-and-white traffic cones are arranged in a row along the top yellow surface of the platform, serving as the target objects. To the lower right of the platform, at a horizontal distance labeled **100 cm**, there is an orange square landing pad marked with a white letter **"H"** (helipad symbol), indicating the designated landing zone for the drone. A double-headed arrow between the platform and the landing pad indicates the 100 cm separation distance.

### Figure 2: Target Object

**Image description:** A close-up illustration of a single target object — a standard traffic cone. The cone is predominantly orange with a horizontal white reflective stripe near the upper portion, mounted on a square black base. A vertical double-headed arrow on the left of the cone indicates its total height as **30 cm**.

---

## 6.0 Level of Autonomy

**6.1** The autonomy consists of 2 levels: **autonomous control with off-board processing** and **autonomous control with onboard processing**. Based on the autonomy level of the drone in a mission element, a coefficient is multiplied by the achieved score of that mission element.

**6.2** Participants must acknowledge the technical committee regarding the level of autonomy implemented in their drone.

**6.3** The technical committee reserves the right to inspect and validate the autonomy level of the participants' drones.

**6.4** The coefficients are defined in the following table:

| Level of Autonomy | Ka (Coefficient) | Comments |
|---|---|---|
| **Autonomous (off-board Process)** | 1 | Control and navigation of the drone is performed autonomously, and some processes are done using a computer outside of the drone itself. *Example: Tello, Parrot Bebop, Hula, LiteBee, etc.* |
| **Autonomous (on-board Process)** | 2 | Control and navigation of the drone is performed autonomously, and all of the processes are done using a computer inside the drone itself. It is capable of reaching its destination even if the router or external computer used for monitoring is shut down. *Example: Coex, Matrice 300* |

---

## 7.0 Method of Scoring

**7.1** The score of a time trial depends on the performance of the drone and the level of autonomy. It will be calculated using the formula below:

```
Trial Record = (Trial time) * bonus / Ka
```

**7.2** The bonus coefficient will be considered **0.8** if the drone lands on the landing pad after finishing the track. If the drone lands outside of the landing pad or doesn't complete the track, it will be equal to **1**.

**7.3 Examples of scoring:**

**A —** An autonomous (offboard) drone completes the track in 20s and lands on the landing pad. The trial record will be:

```
Trial Record = (20) * 0.8 / 1 = 16s
```

**C —** An autonomous (offboard) drone completes the track in 40s and lands outside of the landing pad. The trial record will be:

```
Trial Record = (40) * 1 / 1 = 40s
```

**D —** An autonomous (onboard) drone completes the track in 60s and lands outside of the landing pad. The trial record will be:

```
Trial Record = (60) * 1 / 2 = 30s
```

### Important Notes

- The drone is passed the platform if it goes through it without major collision with the platform (crashing). Minor collision is acceptable if the drone is still airborne.
- The teams are not allowed to add external markers to the field.
- The teams will be ranked based on best final record (**lower is better**).

---

## 8.0 Drone Master Selection

**8.1** The Drone Master Award is given to the team with the highest total score from all Air Drone challenges. Points are awarded based on medal rankings:

- **Gold medal:** 3 points
- **Silver medal:** 2 points
- **Bronze medal:** 1 point

**8.2** If two or more teams have the same total score, the tie is broken by the team with the higher achievement in the **Emergency Delivery and Tower Inspection**.

**8.3** If the tied teams did not participate in the Emergency Delivery and Tower Inspection, the tie is broken based on the highest achievement in the **Autonomous Race**.

---

## 9.0 Amendment

| Year | Version | Location | Changes |
|---|---|---|---|
| | | | |

---

## Appendix: Game Flow Summary (Flowchart Explanation)

Although the original document does not contain an explicit flowchart, the mission sequence described in Section 4.0 can be expressed as the following sequential flow:

1. **Start** — Drone is placed in the designated take-off zone.
2. **Autonomous Take-off** — Drone lifts off without manual control.
3. **Target Identification** — Drone uses computer vision / machine learning to detect and lock onto the assigned target cone (no GPS/GNSS allowed).
4. **Approach Platform** — Drone navigates 1 meter horizontally toward the 1 m tall platform.
5. **Target Hit** — Drone makes intentional, controlled physical contact with the target cone using a non-sharp, non-destructive mechanism while remaining airborne.
6. **Timer Trigger** — The moment the target makes initial contact with the ground, the official race time is recorded.
7. **Landing Sequence (15-second window)** — Drone has up to 15 seconds to land.
   - **If lands on landing pad (H):** Bonus coefficient = **0.8** (better score).
   - **If lands outside pad / incomplete track:** Bonus coefficient = **1.0**.
8. **Score Calculation** — `Trial Record = Trial Time × Bonus / Ka`, where Ka = 1 (off-board) or 2 (on-board).
9. **End of Attempt** — Repeat for up to 3 attempts; the **best (lowest) record** is kept.
