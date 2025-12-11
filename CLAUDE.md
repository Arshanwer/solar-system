# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Interactive 3D solar system visualization built with Three.js r128. This is an educational web application featuring realistic planetary rendering with custom GLSL shaders, interactive camera controls, and child-friendly information about celestial bodies.

**Technology Stack:**
- Three.js r128 (WebGL rendering)
- Custom GLSL shaders for Sun and Earth
- OrbitControls for camera manipulation
- Single-page HTML application (no build system)

## Running the Application

Simply open [solar.html](solar.html) in a modern browser with WebGL support. No installation, build process, or package management required.

**Requirements:**
- Modern browser with WebGL and ES6 JavaScript support
- The `textures/` directory must be in the same location as the HTML file

## Architecture Overview

### File Structure

This is a monolithic single-file application with all code embedded in [solar.html](solar.html):
- **Lines 10-459**: CSS styling and responsive design
- **Lines 461-636**: Custom GLSL shader definitions (Sun and Earth)
- **Lines 638-1967**: JavaScript application logic

### 3D Scene Hierarchy

```
Scene
├── Sun (custom shader with animated simplex noise)
├── Earth (custom day/night shader with shadow blending)
│   └── Moon (orbital pivot system)
├── Mars (orbital pivot)
├── Jupiter (orbital pivot)
├── Starfield (background sphere with milky way texture)
├── Point Light (positioned at Sun)
└── Ambient Light
```

### Major Systems

1. **Shader System**: Custom GLSL shaders
   - Sun: Simplex noise animation with rim lighting and hot spots
   - Earth: Day/night texture blending based on sun direction with realistic shadow transitions

2. **Orbital Mechanics**: Pivot-based orbital system
   - Each planet uses a pivot point for realistic orbital rotation
   - Configurable rotation and orbit speeds
   - Scale compressed ~1000x for exploration (1 unit ≈ 1 AU conceptually)

3. **Input System**: Dual support for desktop and mobile
   - Desktop: single-click (rotate), double-click (info), right-click (travel), mouse wheel (zoom)
   - Mobile: single-tap (rotate), double-tap (info), long-press (travel), pinch (zoom)

4. **Focus System**: Camera transitions between planets
   - Smooth interpolation to target objects
   - Each planet has custom camera configuration (distance, zoom, rotation speed)
   - Free roam mode for unrestricted exploration

5. **Travel System**: Click/tap to fly camera to any location
   - Cubic ease-out interpolation
   - Smooth position and target transitions

6. **Animation Loop**: requestAnimationFrame with time-based updates
   - Configurable time multiplier (0.5x, 1x, 2x, 5x speeds)
   - Pause/resume capability
   - Delta-time based physics

## Code Organization

### Key Constants ([solar.html:640-666](solar.html#L640-L666))
- Planet sizes: `EARTH_RADIUS = 1`, `MOON_RADIUS = 0.27`, `MARS_RADIUS = 0.53`, `JUPITER_RADIUS = 11`
- Orbital radii: `MOON_ORBIT_RADIUS = 60`, `MARS_ORBIT_RADIUS = 75`, `JUPITER_ORBIT_RADIUS = 260`
- Rotation and orbit speeds for each celestial body
- Long press duration: 600ms, double tap duration: 300ms

### Global State ([solar.html:669-714](solar.html#L669-L714))
- Scene objects: `scene`, `camera`, `renderer`, `controls`, `earth`, `moon`, `sun`, `mars`, `jupiter`
- Control state: `timeMultiplier`, `isAnimationPaused`, `shadowsEnabled`
- Interaction state: travel system, focus system, touch/mouse event handling
- Focus state object with transition tracking

### Configuration Objects

**Camera Configurations** ([solar.html:717-760](solar.html#L717-L760)):
- Presets for each focus mode (sun, earth, moon, mars, jupiter, free)
- Each config includes: distance, minDistance, maxDistance, rotateSpeed, zoomSpeed

**Object Information Database** ([solar.html:762-825](solar.html#L762-L825)):
- Child-friendly educational content for each celestial body
- Includes: name, type, size, temperature, distance, color, fun facts, descriptions

### Core Functions

- `init()` ([solar.html:827-993](solar.html#L827-L993)): Scene setup, object creation, event listeners
- `animate()` ([solar.html:1884-1940](solar.html#L1884-L1940)): Main render loop with time-based updates
- `setFocus(mode, object)` ([solar.html:1762-1854](solar.html#L1762-L1854)): Camera focus transitions
- `travelToClickPoint(x, y)` ([solar.html:1529-1634](solar.html#L1529-L1634)): Interactive camera movement
- `handleInfoRequest(x, y)` ([solar.html:1451-1527](solar.html#L1451-L1527)): Display planet information panels
- `toggleShadows()` ([solar.html:1698-1715](solar.html#L1698-L1715)): Enable/disable realistic lighting

## Development Patterns

### Making Changes

- **CSS modifications**: Edit `<style>` block ([solar.html:10-459](solar.html#L10-L459))
- **JavaScript logic**: Modify `<script>` block ([solar.html:638-1962](solar.html#L638-L1962))
- **Planet information**: Update `objectInfo` object ([solar.html:762-825](solar.html#L762-L825))
- **Physics/constants**: Adjust constants section ([solar.html:640-654](solar.html#L640-L654))
- **Shader modifications**: Edit shader script blocks ([solar.html:461-636](solar.html#L461-L636))

### Texture Dependencies

All textures must be in the `textures/` directory:
- `2k_sun.jpg`
- `2k_earth_daymap.jpg`, `2k_earth_nightmap.jpg`, `2k_earth_normal_map.tif`, `2k_earth_specular_map.jpg`
- `2k_moon.jpg`
- `2k_mars.jpg`
- `2k_jupiter.jpg`
- `2k_stars_milky_way.jpg`

### Responsive Design

The application includes mobile-responsive layouts:
- Tablet breakpoint: 768px ([solar.html:335-396](solar.html#L335-L396))
- Small mobile breakpoint: 480px ([solar.html:398-459](solar.html#L398-L459))
- Touch event handling separate from mouse events
- Adaptive UI layouts using CSS flexbox and grid
