# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Interactive 3D solar system visualization built with Three.js r128. This is an educational web application featuring realistic planetary rendering with custom GLSL shaders, interactive camera controls, and child-friendly UI optimized for iPad.

**Complete Solar System:**
- All 8 planets: Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune
- Earth's Moon
- The Sun (with custom animated shader)
- Saturn's rings with transparency
- Realistic Milky Way starfield background

**Technology Stack:**
- Three.js r128 (WebGL rendering)
- Custom GLSL shaders for Sun and Earth
- OrbitControls for camera manipulation
- Single-page HTML application (no build system)
- Child-friendly UI with colorful emojis and gradients

## Running the Application

Simply open [solar.html](solar.html) in a modern browser with WebGL support. No installation, build process, or package management required.

**Requirements:**
- Modern browser with WebGL and ES6 JavaScript support
- The `textures/` directory must be in the same location as the HTML file
- **Important**: Must run via local server (not file://) to avoid CORS issues with textures

**Recommended**: Use a local server like `python3 -m http.server` or VS Code Live Server

## Architecture Overview

### File Structure

This is a monolithic single-file application with all code embedded in [solar.html](solar.html):
- **Lines 10-600+**: CSS styling, responsive design, and child-friendly UI styles
- **Lines 650-750**: Custom GLSL shader definitions (Sun and Earth)
- **Lines 838-2637**: JavaScript application logic (2,637 total lines)

### 3D Scene Hierarchy

```
Scene
├── Sun (custom shader with animated simplex noise)
├── Mercury (orbital pivot)
├── Venus (orbital pivot)
├── Earth (custom day/night shader with shadow blending)
│   └── Moon (orbital pivot around Earth)
├── Mars (orbital pivot)
├── Jupiter (orbital pivot)
├── Saturn (orbital pivot)
│   └── Saturn Ring (transparent ring system)
├── Uranus (orbital pivot)
├── Neptune (orbital pivot)
├── Starfield (background sphere with milky way texture)
├── Point Light (positioned at Sun)
└── Ambient Light
```

**Key Pattern**: All planets use orbital pivot system for accurate Kepler mechanics

### Major Systems

1. **Shader System**: Custom GLSL shaders
   - Sun: Simplex noise animation with rim lighting and hot spots
   - Earth: Day/night texture blending based on sun direction with realistic shadow transitions

2. **Orbital Mechanics**: Pivot-based orbital system
   - Each planet uses a pivot point for realistic orbital rotation
   - Astronomically accurate orbital speeds (relative to Earth's 365-day year)
   - Scale compressed ~1000x for exploration (1 unit ≈ 1 AU conceptually)
   - Moon orbits Earth (not the Sun)

3. **Input System**: Dual support for desktop and mobile
   - Desktop: single-click (rotate), double-click (info), right-click (travel), mouse wheel (zoom)
   - Mobile: single-tap (rotate), double-tap (info), long-press (travel), pinch (zoom)

4. **Focus System**: Camera transitions between planets
   - Smooth interpolation to target objects
   - Each planet has custom camera configuration (distance, zoom, rotation speed)
   - Free roam mode for unrestricted exploration
   - 11 focus modes: Sun, Mercury, Venus, Earth, Moon, Mars, Jupiter, Saturn, Uranus, Neptune, Free Roam

5. **Travel System**: Click/tap to fly camera to any location
   - Cubic ease-out interpolation
   - Smooth position and target transitions

6. **Animation Loop**: requestAnimationFrame with time-based updates
   - Configurable time multiplier (0.5x, 1x, 2x, 5x speeds)
   - Pause/resume capability
   - Delta-time based physics

7. **Child-Friendly UI System**: Kid-optimized interface
   - Colorful planet buttons with planet-specific gradient colors
   - Large emoji icons for each celestial body
   - Fun time controls (🐌 Slow, ▶️ Normal, ⏩ Fast, ⚡ Super Fast)
   - Enhanced info panels with emoji icons (🔭 📏 🌡️ 🎨 📍 ✨ 📖)
   - Bounce and glow animations
   - iPad-specific responsive optimizations

## Code Organization

### Key Constants ([solar.html:838-875](solar.html#L838-L875))

**Planet Sizes (relative to Earth):**
- `EARTH_RADIUS = 1` (baseline)
- `MOON_RADIUS = 0.27`
- `MERCURY_RADIUS = 0.383`
- `VENUS_RADIUS = 0.95`
- `MARS_RADIUS = 0.53`
- `JUPITER_RADIUS = 11`
- `SATURN_RADIUS = 9.45`
- `URANUS_RADIUS = 4.0`
- `NEPTUNE_RADIUS = 3.88`
- `SUN_RADIUS = 5`

**Orbital Radii (compressed scale):**
- `MOON_ORBIT_RADIUS = 60` (around Earth)
- `MERCURY_ORBIT_RADIUS = 19.5`
- `VENUS_ORBIT_RADIUS = 36`
- Earth at 50 units from Sun
- `MARS_ORBIT_RADIUS = 75`
- `JUPITER_ORBIT_RADIUS = 260`
- `SATURN_ORBIT_RADIUS = 477`
- `URANUS_ORBIT_RADIUS = 960`
- `NEPTUNE_ORBIT_RADIUS = 1502.5`

**Orbital Speeds (astronomically accurate, relative to Earth):**
- `EARTH_ORBIT_SPEED = 0.0002` (baseline: 365 days)
- `MOON_ORBIT_SPEED = 0.00268` (27.3 days around Earth, 13.4x faster)
- `MERCURY_ORBIT_SPEED = 0.00083` (88 days, 4.15x faster)
- `VENUS_ORBIT_SPEED = 0.000324` (225 days, 1.62x faster)
- `MARS_ORBIT_SPEED = 0.000106` (687 days, 0.53x slower)
- `JUPITER_ORBIT_SPEED = 0.000017` (11.86 years, 0.084x)
- `SATURN_ORBIT_SPEED = 0.0000068` (29.46 years, 0.034x)
- `URANUS_ORBIT_SPEED = 0.0000024` (84 years, 0.012x)
- `NEPTUNE_ORBIT_SPEED = 0.0000012` (164.8 years, 0.006x)

**Interaction Settings:**
- Long press duration: 600ms
- Double tap duration: 300ms
- Long press move threshold: 10px

### Global State

Scene objects: `scene`, `camera`, `renderer`, `controls`, `sun`, `mercury`, `venus`, `earth`, `moon`, `mars`, `jupiter`, `saturn`, `saturnRing`, `uranus`, `neptune`, `starfield`

Orbital pivots: `moonOrbitPivot`, `mercuryOrbitPivot`, `venusOrbitPivot`, `marsOrbitPivot`, `jupiterOrbitPivot`, `saturnOrbitPivot`, `uranusOrbitPivot`, `neptuneOrbitPivot`

Control state:
- `timeMultiplier` (0, 0.5, 1, 2, 5)
- `isAnimationPaused` (boolean)
- `shadowsEnabled` (boolean)

Interaction state:
- Travel system state
- Focus system state
- Touch/mouse event handling state

### Configuration Objects

**Camera Configurations** ([solar.html:949-1028](solar.html#L949-L1028)):
- Presets for each focus mode: sun, mercury, venus, earth, moon, mars, jupiter, saturn, uranus, neptune, free
- Each config includes: distance, minDistance, maxDistance, rotateSpeed, zoomSpeed

**Object Information Database** ([solar.html:1030-1152](solar.html#L1030-L1152)):
- Child-friendly educational content for each celestial body
- Includes: name, type, size, temperature, distance, color, fun facts, descriptions
- All 10 celestial bodies documented: sun, mercury, venus, earth, moon, mars, jupiter, saturn, uranus, neptune

### Core Functions

- `init()` ([solar.html:1154+](solar.html#L1154)): Scene setup, planet creation, event listeners
- `animate()` ([solar.html:2524+](solar.html#L2524)): Main render loop with time-based updates for all 8 planets
- `setFocus(mode, object)` ([solar.html:2397+](solar.html#L2397)): Camera focus transitions
- `showInfoPanel(objectType)` ([solar.html:2349+](solar.html#L2349)): Display planet information with emoji icons
- `travelToClickPoint(x, y)`: Interactive camera movement
- `handleInfoRequest(x, y)`: Raycasting and info panel triggering
- `toggleShadows()`: Enable/disable realistic lighting

### Child-Friendly UI Components

**Planet Buttons:**
- Class: `.planet-btn` with planet-specific color classes
- Each button has planet emoji and name
- Gradient backgrounds matching actual planet colors
- Active state with glow animation
- Tap/click bounce animation

**Time Controls:**
- 🐌 Slow (0.5x)
- ▶️ Normal (1x)
- ⏩ Fast (2x)
- ⚡ Super Fast (5x)
- ⏸️ Pause

**Info Panel Enhancements:**
- Planet emoji in title
- Icon labels: 🔭 What is it? 📏 Size, 🌡️ Temperature, 🎨 Color, 📍 Distance
- ✨ Cool Fact section with golden highlight
- 📖 Tell me more section

## Development Patterns

### Making Changes

- **CSS modifications**: Edit `<style>` block (lines 10-600+)
- **JavaScript logic**: Modify `<script>` block (lines 838-2637)
- **Planet information**: Update `objectInfo` object ([solar.html:1030-1152](solar.html#L1030-L1152))
- **Orbital speeds**: Adjust speed constants ([solar.html:838-875](solar.html#L838-L875))
- **Camera focus configs**: Update `cameraConfigs` object ([solar.html:949-1028](solar.html#L949-L1028))
- **Shader modifications**: Edit shader script blocks (lines 650-750)

### Adding a New Planet

1. Add constants (radius, orbit radius, rotation speed, orbit speed)
2. Create orbital pivot and planet mesh in `init()`
3. Load texture from `textures/` directory
4. Add to animation loop with rotation and orbit
5. Add camera configuration to `cameraConfigs`
6. Add educational info to `objectInfo`
7. Add focus button to HTML
8. Update CLAUDE.md with new planet

### Texture Dependencies

All textures must be in the `textures/` directory:
- `2k_sun.jpg`
- `2k_mercury.jpg`
- `2k_venus.jpg`
- `2k_earth_daymap.jpg`, `2k_earth_nightmap.jpg`, `2k_earth_normal_map.tif`, `2k_earth_specular_map.jpg`
- `2k_moon.jpg`
- `2k_mars.jpg`
- `2k_jupiter.jpg`
- `2k_saturn.jpg`, `2k_saturn_ring_alpha.png`
- `2k_uranus.jpg`
- `2k_neptune.jpg`
- `2k_stars_milky_way.jpg`

### Responsive Design

The application includes mobile-responsive layouts:
- iPad/Tablet breakpoint: 768px-1024px (optimized for child-friendly UI)
- General tablet breakpoint: max-width 768px
- Small mobile breakpoint: max-width 480px
- Touch event handling separate from mouse events
- Adaptive UI layouts using CSS flexbox and grid
- Larger touch targets (44-48px minimum) for iPad

### UI Color Scheme

Planet button gradients (matching actual planet appearance):
- Sun: Orange gradient (#FFAA00 to #FF6B00)
- Mercury: Gray gradient (#999999 to #666666)
- Venus: Yellow/cream gradient (#FFC870 to #FFB84D)
- Earth: Blue gradient (#4A90E2 to #2E5C8A)
- Moon: Silver gradient (#C0C0C0 to #A0A0A0)
- Mars: Red/orange gradient (#FF6B4A to #CC4A2E)
- Jupiter: Orange/tan gradient (#FFB366 to #FF9933)
- Saturn: Golden gradient (#FFD700 to #CCAA00)
- Uranus: Cyan gradient (#4FD0E0 to #00A0C0)
- Neptune: Deep blue gradient (#4169E1 to #2048A0)
- Free Roam: Purple gradient (#9B59B6 to #8E44AD)

## Important Notes

- **CORS Issues**: File must be served via HTTP server, not opened directly (file://)
- **Orbital Accuracy**: All orbital speeds are astronomically accurate relative to Earth's 365-day year
- **Moon Behavior**: Moon orbits Earth, not the Sun (moonOrbitPivot is child of Earth system)
- **Saturn Rings**: Use transparent texture with alpha channel for realistic appearance
- **Performance**: Single-file design keeps everything simple but file is large (2,600+ lines)
- **Child-Friendly Design**: UI optimized for ages 6-12, especially on iPad
