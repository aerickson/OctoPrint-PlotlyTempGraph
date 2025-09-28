# Plotly.js v1 to v2 Upgrade Analysis

## Overview
This document provides a comprehensive analysis of the effort required to upgrade the OctoPrint-PlotlyTempGraph plugin from Plotly.js v1.58.4 to v2.x.

## Current State
- **Current Version**: Plotly.js v1.58.4 (released 2020)
- **Target Version**: Plotly.js v2.x (latest)
- **Plugin Type**: OctoPrint temperature graphing plugin
- **Primary Language**: JavaScript with KnockoutJS

## Breaking Changes Analysis

### 🔴 Critical Issues (Must Fix)

#### 1. Color Library Dependency
**Location**: Lines 426, 493 in `plotlytempgraph.js`
```javascript
points.line.color = pusher.color(name_map_color).tint(0.5).html();
```

**Problem**: The `pusher.color()` function is not available in Plotly.js v2
**Impact**: Runtime errors, broken target line coloring
**Effort**: 4-6 hours
**Solution**: 
- Add a color manipulation library (tinycolor2, chroma-js, or custom implementation)
- Replace 4 instances of `pusher.color().tint(0.5).html()` calls

#### 2. Library File Update
**Files**: 
- `octoprint_plotlytempgraph/static/js/plotly-latest.min.js`
- `octoprint_plotlytempgraph/__init__.py` (asset references)

**Effort**: 1-2 hours
**Tasks**:
- Download Plotly.js v2 minified file
- Update asset references in Python plugin file
- Test basic functionality

### 🟡 Potential Issues (Verify & Fix)

#### 3. Subplot Configuration
**Location**: Lines 1034-1036 in `resize_graph_height()` function
```javascript
self.layout.yaxis.domain = [0, 0.5];
self.layout.yaxis2 = { 
    type:"linear", 
    domain: [0.5, 1], 
    yaxis: 'y2' 
};
```

**Risk**: Medium
**Effort**: 2-4 hours
**Testing Required**: Dual-axis temperature graphs, subplot positioning

#### 4. Layout Property Compatibility
**Areas of Concern**:
- Grid layout system (`self.layout.grid`)
- Margin specifications
- Axis configuration properties
- Image overlay positioning

**Risk**: Low-Medium
**Effort**: 1-3 hours

#### 5. Hover Template Syntax
**Location**: Custom hover templates in settings
```javascript
return '<b>%{customdata:.1f}&#8457;</b> ';
return '<b>%{y:.1f}&#8451;</b> ';
```

**Risk**: Low
**Effort**: 1-2 hours

### 🟢 Low Risk Items

#### 6. Core Plotly API Methods
These methods are expected to remain compatible:
- ✅ `Plotly.newPlot()`
- ✅ `Plotly.addTraces()`
- ✅ `Plotly.extendTraces()`
- ✅ `Plotly.relayout()`

**Effort**: 0-2 hours (verification only)

## Implementation Plan

### Phase 1: Core Dependencies (4-6 hours)
1. **Replace Plotly.js Library**
   - Download Plotly.js v2.x
   - Update `plotly-latest.min.js`
   - Update asset references in `__init__.py`

2. **Fix Color Manipulation**
   - Choose color library (recommended: tinycolor2)
   - Add to project dependencies
   - Replace `pusher.color().tint(0.5).html()` calls
   - Test target line styling

### Phase 2: Compatibility Testing (4-6 hours)
1. **Layout Verification**
   - Test basic single-axis graphs
   - Verify dual-axis subplot functionality
   - Check margin and sizing
   - Validate background image positioning

2. **Feature Testing**
   - Temperature data plotting
   - Real-time updates
   - Legend toggle functionality
   - Settings panel interactions

### Phase 3: Edge Cases & Polish (2-4 hours)
1. **Advanced Features**
   - Custom hover templates
   - Fahrenheit conversion
   - Multiple temperature sensors
   - Graph cutoff functionality

2. **Integration Testing**
   - Test with various OctoPrint configurations
   - Verify plugin settings persistence
   - Check UI Customizer plugin compatibility

## Effort Estimation

| Phase | Minimum | Maximum | Most Likely |
|-------|---------|---------|-------------|
| Phase 1 | 4 hours | 6 hours | 5 hours |
| Phase 2 | 4 hours | 6 hours | 5 hours |
| Phase 3 | 2 hours | 4 hours | 3 hours |
| **Total** | **10 hours** | **16 hours** | **13 hours** |

## Risk Assessment

### High Risk 🔴
- **Color manipulation library dependency** - Will definitely break without fix

### Medium Risk 🟡  
- **Subplot domain configuration** - May need updates for dual-axis graphs
- **Layout property changes** - Some properties might be deprecated

### Low Risk 🟢
- **Basic plotting functionality** - Core API is stable
- **Trace properties** - Line plot properties should remain compatible

## Recommended Approach

### Color Library Options
1. **TinyColor2** (Recommended)
   - Small, focused library
   - Drop-in replacement for color manipulation
   - Good browser support

2. **Chroma.js**
   - More features, slightly larger
   - Excellent color manipulation capabilities

3. **Custom Implementation**
   - Minimal code for just tint functionality
   - No external dependencies

### Testing Strategy
1. **Unit Testing**: Test color manipulation functions
2. **Integration Testing**: Verify all graph types work
3. **Regression Testing**: Compare v1 vs v2 output
4. **User Acceptance Testing**: Test with real temperature data

## Benefits of Upgrading

### Security & Maintenance
- **Security Updates**: Plotly.js v1.58.4 is 4+ years old
- **Bug Fixes**: Numerous fixes in v2.x releases
- **Long-term Support**: v1.x is end-of-life

### Performance & Features
- **Better Performance**: v2.x has performance improvements
- **New Features**: Access to latest Plotly.js capabilities
- **Modern Browser Support**: Better compatibility with newer browsers

## Conclusion

This upgrade is **highly recommended** despite the moderate effort required. The main challenge is replacing the color manipulation functionality, but once resolved, the upgrade provides significant benefits in terms of security, performance, and long-term maintainability.

The estimated 10-16 hours of development time is a reasonable investment for bringing this plugin up to modern standards and ensuring its continued functionality with current web browsers and security requirements.