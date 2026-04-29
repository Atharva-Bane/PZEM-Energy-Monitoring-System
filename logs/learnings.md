# Key Learnings

This document summarizes the major insights and understanding gained during the development of this project.

---

## 1. System Design Matters More Than Tools

A well thought-out architecture is more important than adding multiple components.

The removal of Raspberry Pi simplified the system and significantly improved reliability.

---

## 2. Simplicity Improves Stability

Initial designs tend to be overcomplicated.

A minimal and focused architecture:

* Is easier to debug
* Is more reliable
* Performs better in real world conditions

---

## 3. Most IoT Problems Are Network Problems

Many issues faced during development were related to:

* Incorrect configurations
* Connectivity issues
* Topic mismatches

This highlights the importance of understanding networking fundamentals in IoT systems.

---

## 4. Hardware Behavior Must Be Verified

Assumptions about hardware can lead to bugs.

Example:

* Relay module behaved as active-low
* Required logic inversion in firmware

---

## 5. Integration Is the Hardest Part

Individual components may work perfectly, but integrating them introduces complexity.

Challenges arise when:

* Hardware interacts with software
* Multiple systems communicate together

---

## 6. Data Representation Matters

Displaying stale or incorrect data can mislead users.

Ensuring real time synchronization between system state and UI is critical.

---

## 7. Practical Hardware Design Is Important

Real world usability matters.

Designing a socket based solution for the PZEM coil improved:

* Safety
* Maintainability
* Ease of use

---

## 8. Iteration Is the Key to Improvement

The system improved through continuous testing, debugging, and redesign.

Each issue helped refine the final architecture.

---

## Final Thought

This project demonstrated that building a working system is not just about coding or hardware, it is about making correct decisions at every stage.
