# Moop Trash Harpoon - Design Specification

## Overview

A lightweight harpoon device for drone-mounted waste collection. Designed to fire a tethered projectile to snag lightweight plastic waste (e.g. crisp packets) and retrieve it.

## Coordinate System

- **x** (axial): 0 = muzzle/firing end, 1 = drone attachment end
- **r** (radial): 0 = centerline, 1 = outer surface
- **t** (angular): 0 = up (12 o'clock), 0.25 = right, 0.5 = down, 0.75 = left, 1 = up (wraps)

## Dimensions

*To be defined*

- Overall length (x): TBD mm
- Overall radius (r=1): TBD mm

## Subsystems

### 1. Barrel
*The tube the projectile travels through*

- Position: x=0 to x=TBD
- Geometry: TBD

### 2. Gripper
*The grabbing mechanism - sits external to main body at x=0*

- Position: x ≤ 0 (external, flush with muzzle)
- Design: TBD (multiple gripper designs anticipated)
- Interface: Attaches to tether at x=0
- Note: Gripper never enters the cylinder; modular/swappable

**States:**
- **Ready**: Will grip when it senses an object (mechanism TBD per gripper design)
- **Release**: Drops payload

**Release trigger:**
- Reed switch in gripper, magnet in trash bin
- Proximity-based: gripper releases when close to bin
- No electrical connection to drone required
- Gripper is fully independent module

*Gripper design is a separate workstream - this spec covers the launcher body only.*

### 3. Tether
*Line connecting gripper to spool*

- Type: Dental tape (flat filament) for prototyping; alternatives include braided fishing line
- Properties: Lightweight, thin, high tensile strength, low cost
- Length: TBD (function of firing range)
- Note: Flat profile may aid neat winding on spool
- Note: No conductivity required - gripper release is proximity-triggered via reed switch

### 4. Spool & Retrieval Motor
*Tether storage, winding, and cocking mechanism*

- Motor orientation: Perpendicular to barrel (shaft along r axis)
- Motor position: Near x=1, t=TBD (depends on drone mounting)
- Spool: Rotates around radial axis
- Filament path: Travels along x-axis at r=0, redirected via guide/eyelet to wrap on spool
- Functions:
  - Reel in filament (retrieval and cocking)
  - Unspool at high speed during fire
  - Pull shuttle back to cock spring

### 5. Filament Path
*Route from spool to gripper*

- Filament runs from spool at x=1, through chamber, along central axis (r=0), through spring, through shuttle, to gripper at x=0
- Pre-fire: motor unspools D+Se into chamber
- During fire: filament pays out from chamber with zero resistance

### 6. Shuttle
*Moving element that transfers spring energy to gripper*

- Length: Se - Sc
- Position (cocked): x = Se-Sc (rear) to x = 0 (front)
- Position (fired): rear at x = 0 (spring fully extended)
- Structure: Simple tube with filament channel at r=0

**Floating design (zero barrel friction):**
- Shuttle body diameter smaller than barrel inner diameter (clearance gap)
- Shuttle suspended by spring attachment at rear - does not touch barrel wall
- Spring keeps shuttle centered/coaxial
- Filament through center also aids alignment
- Small lip/flange at front (x=0 end) protrudes to catch on spike
- Lip radius > shuttle body radius, but still < barrel inner radius

**Lip geometry:**
- Face toward x=1 (aft): Diagonal/ramped - slides smoothly past spike during re-cocking
- Face toward x=0 (forward): Square 90° - catches against spike when cocked

**Result:** During firing, shuttle accelerates through air with zero sliding contact. During re-cocking, ramped surfaces guide lip past spike smoothly.

### 7. Trigger (Piezo Spikes)
*Gate mechanism at muzzle that holds and releases shuttle*

- Position: x = 0, mounted on barrel wall
- Quantity: 3 spikes at t=0, t=0.33, t=0.67 (120° intervals)
- Components: 3 piezo bender strips, each with small spike protruding toward r=0
- Spike height: ~2mm (just enough to catch shuttle lip)
- Wiring: All 3 piezos in parallel - single voltage pulse opens all simultaneously

**Spike geometry:**
- Face toward x=1 (aft): Square 90° - catches against shuttle lip's square face when cocked
- Face toward x=0 (forward): Diagonal/ramped - slides smoothly against lip during re-cocking

**Symmetry:** Three evenly-spaced spikes provide minimum symmetric support, balancing forces on shuttle lip

**States:**
- **Blocking (piezos relaxed):** Spikes protrude across shuttle path, hold shuttle against spring pressure
- **Open (piezos energised):** Piezos bend outward, spikes fully clear shuttle path (no contact during firing)

**Firing:**
1. Voltage applied to piezo
2. Piezo bends → spike moves fully beyond shuttle's outer radius
3. Spring fires, shuttle accelerates through with zero friction
4. After suitable time (shuttle lip has passed spike position), piezo relaxes, spike returns to blocking position

**Re-cocking (passive):**
1. Motor pulls shuttle back toward x=1 (slow, controlled)
2. Shuttle edge contacts square face of spike
3. Shuttle pushes spike outward (piezo flexes passively)
4. Shuttle passes spike
5. Piezo springs back → spike returns to blocking position
6. Spring holds shuttle against spike

**Advantages:**
- Very light (fraction of a gram)
- Very fast (milliseconds)
- Very low power (capacitive, only draws current during transition)
- Solid state (no wear)
- Re-cocking is passive
- Zero friction during firing
- Dual-purpose: piezos act as both actuators and sensors

**Latch sensing (dual confirmation):**
- Motor current sensing: detects when something stops moving (current spike)
- Piezo voltage sensing: piezos generate voltage when flexed by shuttle lip
- **Successful latch:** Current spike AND piezo voltage pulse → stop motor
- **Snag/obstruction:** Current spike but NO piezo pulse → gripper caught on something, trigger error handling

### 8. Spring
*Energy storage for firing*

- Type: Compression spring
- Mount: Rear anchored at x=Se
- Front: Attached to shuttle (prevents shuttle exiting barrel)
- Length extended: Se
- Length compressed: Sc
- Stroke: Se - Sc
- Filament path: Runs through spring center at r=0

### 9. Filament Chamber
*Staging area for pre-measured filament*

- Position: x=Se to x=1 (between spring anchor and spool)
- Function: Holds unspooled filament in loose coils before firing
- Pre-staged length: D + Se (where D = distance to target)
- Benefit: Zero spool resistance during firing

### 10. Mount
*Attachment to drone*

- Position: x=1
- Interface: TBD

## Design Notes

**Design elegance:**
- Two active components: motor (spool/retrieval/cocking) and piezo trigger
- Shuttle is a simple tube - no internal mechanisms
- Trigger re-cocking is passive (shuttle pushes spike aside)
- Gripper is fully independent module with magnetic release (reed switch + bin magnet)
- No electrical connection through tether required
- Tether can be simple dental tape or fishing line
- Pre-staged filament chamber = zero resistance during firing

**Key volumes:**
- Shuttle: simple tube, length Se-Sc, travels from x=0 to x=Se-Sc
- Spring: anchored at x=Se, extends toward x=0
- Chamber: x=Se to x=1
- Spool/motor: near x=1, perpendicular mount
- Trigger: at x=0, small piezo bender on barrel wall

**Complete operational cycle:**

1. **Cocked, ready:** Shuttle at x=Se-Sc, held by spike, spring compressed, gripper at x=0
2. **Acquire target:** Drone positions, estimates distance D
3. **Pre-fire:** Motor unspools D+Se filament into chamber
4. **Fire:** Piezo energised → spike clears → spring fires → gripper launches
5. **Flight:** Filament pays out from chamber (zero drag), gripper reaches target
6. **Grab:** Gripper snags waste (gripper-specific mechanism)
7. **Retrieve:** Motor reels in filament, gripper + waste return to x=0
8. **Return to base:** Drone flies to bin
9. **Release:** Gripper passes over bin magnet → reed switch triggers → gripper releases waste
10. **Re-cock:** Motor continues reeling → shuttle pulled back → shuttle pushes past spike (piezo flexes passively) → spike springs back → system ready

---

*Document version: 0.1*
*Last updated: 2026-01-28*
