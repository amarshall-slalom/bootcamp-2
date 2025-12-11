# UI Guidelines - TODO Application

## Overview
This document outlines the user interface design guidelines for the TODO application, following Material Design principles to create a familiar, intuitive experience similar to Android applications.

## Material Design Principles

### Design Philosophy
Material Design is Google's design system that creates a visual language synthesizing classic principles of good design with innovation and technology. It emphasizes:
- **Tangible surfaces**: UI elements behave like physical materials with depth, shadows, and realistic motion
- **Bold, graphic, intentional**: Deliberate use of color, imagery, typography, and whitespace
- **Motion provides meaning**: Animations are purposeful and guide user attention

### Core Concepts

**Material Surfaces**
- Elements appear as sheets of material that can overlap and cast shadows
- Elevation (z-axis) creates hierarchy through shadow depth
- TODO lists and cards should feel like paper resting on a surface

**Responsive Elevation & Shadows**
- Resting elevation: Base shadow for cards and containers (2-4dp)
- Raised elevation: Interactive elements on hover (4-8dp)
- Higher elevation: Modals and dialogs (16-24dp)

## Layout & Structure

### Grid System
- Use an 8dp grid system for consistent spacing
- Content should align to the grid for visual harmony
- Margins: 16dp on mobile, 24dp on tablet/desktop
- Padding: 16dp inside cards and containers

### Component Hierarchy
1. **App Bar** (Top)
   - Fixed at top with elevation
   - Contains app title and primary actions
   - Background: Primary color
   - Height: 56dp mobile, 64dp desktop

2. **List Container** (Main Content)
   - Background: Surface color (typically white/light gray)
   - Contains TODO items in a vertical list
   - Spacing between items: 8dp

3. **Floating Action Button (FAB)** (Bottom Right)
   - Primary action: Add new TODO
   - Position: 16dp from bottom and right edges
   - Size: 56dp diameter
   - Elevation: 6dp (rises to 12dp on hover)

## Color System

### Color Palette
Material Design uses a primary, secondary, and surface color scheme:

**Primary Color** (Main brand color)
- Used for app bar, FAB, and primary actions
- Suggested: Blue (#2196F3) or customize to brand
- Primary variant: Darker shade for contrast

**Secondary Color** (Accent)
- Used for highlights, switches, and secondary actions
- Suggested: Amber (#FFC107) or complementary to primary
- Draws attention to specific actions

**Surface Colors**
- Background: #FAFAFA (light gray)
- Surface: #FFFFFF (white) for cards
- Error: #F44336 (red) for delete actions

**Text Colors**
- High emphasis: rgba(0,0,0,0.87) - Primary content
- Medium emphasis: rgba(0,0,0,0.60) - Secondary content
- Disabled: rgba(0,0,0,0.38) - Inactive elements

## Typography

### Type Scale
Material Design uses a standardized type scale:

- **H5 (24sp)**: App title in app bar
- **H6 (20sp)**: List names, section headers
- **Body 1 (16sp)**: TODO item text
- **Body 2 (14sp)**: Secondary information, timestamps
- **Caption (12sp)**: Helper text, item counts

### Font Family
- Roboto is the standard Material Design font
- Use system fonts for web: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto
- Fallback to sans-serif

## Interactive Components

### TODO Item Card/List Item
- Minimum height: 48dp (touch target size)
- Padding: 16dp horizontal, 12dp vertical
- Background: Surface color (#FFFFFF)
- Elevation: 1dp (rises to 2dp on hover)
- Border radius: 4dp for subtle rounded corners
- Checkbox position: Left side, vertically centered
- Delete icon: Right side, vertically centered
- Ripple effect on tap/click

### Checkbox Behavior
- Size: 24x24dp
- Unchecked: Gray border (#757575)
- Checked: Primary color fill with white checkmark
- Transition: 200ms ease-in-out
- Checked items: Text gets strikethrough and medium emphasis color

### Buttons & Actions

**Floating Action Button (FAB)**
- Circular button with icon
- Icon: "+" or "add" symbol
- Shadow elevation: 6dp resting, 12dp active
- Ripple effect on click
- Color: Secondary or Primary color

**Icon Buttons**
- Size: 48x48dp (touch target)
- Icon size: 24x24dp
- Ripple effect: Circular, bounded
- Color: Medium emphasis text color

**Text Buttons**
- Uppercase text
- Padding: 8dp vertical, 16dp horizontal
- Ripple effect on click
- No background in default state

## Input Fields

### Text Input (Add TODO)
- Height: 56dp
- Label: Floating label that moves up when focused
- Border: Bottom line in default state, full outline optional
- Focus state: Primary color border, 2dp thickness
- Helper text: Below field in caption size
- Padding: 16dp horizontal

## List Management UI

### List Selector/Drawer
- Navigation drawer slides from left
- Width: 280dp on mobile, 320dp on tablet
- Background: Surface color
- Elevation: 16dp (appears above main content)
- List items: 48dp height with 16dp padding
- Active list: Highlighted with primary color tint
- Divider: Between sections, 1dp line, rgba(0,0,0,0.12)

### List Item in Drawer
- Icon: Left side (list icon)
- Primary text: List name (Body 1)
- Secondary text: Item count (Body 2)
- Ripple on selection

## Motion & Animation

### Animation Principles
- **Duration**: 
  - Simple: 100ms
  - Moderate: 200-300ms
  - Complex: 400-500ms
- **Easing**: Use deceleration for entering, acceleration for exiting
- **Choreography**: Elements move in logical sequence, not all at once

### Specific Animations
- **Adding TODO**: Fade in + slide up (200ms)
- **Deleting TODO**: Fade out + slide right (250ms)
- **Checking off**: Checkmark draw animation (150ms)
- **List transition**: Crossfade content (300ms)
- **FAB**: Scale up on press, with ripple effect
- **Drawer**: Slide in from left (250ms)

## Accessibility

### Touch Targets
- Minimum size: 48x48dp for all interactive elements
- Spacing: At least 8dp between touch targets

### Color Contrast
- Text on background: Minimum 4.5:1 ratio
- Large text: Minimum 3:1 ratio
- Use Material Design's color system for guaranteed compliance

### Screen Reader Support
- Label all interactive elements
- Provide descriptive text for icons
- Announce state changes (item completed, deleted, etc.)

### Keyboard Navigation
- Tab order follows visual hierarchy
- Enter key activates buttons
- Space toggles checkboxes
- Focus visible with outline

## Responsive Design

### Breakpoints
- Mobile: 0-599dp
- Tablet: 600-839dp
- Desktop: 840dp+

### Layout Adaptations
- **Mobile**: Single column, drawer navigation
- **Tablet**: Larger margins (24dp), consider side panel
- **Desktop**: Multi-column optional, permanent navigation panel

## Feedback & States

### Visual Feedback
- **Hover**: Slight elevation increase, background tint
- **Active/Pressed**: Ripple effect, deeper elevation
- **Focus**: Outline or highlight in primary color
- **Disabled**: Reduced opacity (38%), no interaction
- **Loading**: Circular progress indicator (primary color)

### Success States
- **Item added**: Brief snackbar confirmation
- **Item deleted**: Snackbar with undo action
- **List created**: Immediate navigation to new list

### Error States
- **Failed action**: Red snackbar with error message
- **Empty state**: Helpful illustration + message
- **No network**: Clear message with retry action

## Implementation Notes

### CSS Considerations
- Use CSS custom properties for theming
- Implement elevation with box-shadow
- Use transitions for smooth state changes
- Consider CSS Grid for responsive layouts

### Component Libraries
- Material-UI (MUI) for React is recommended
- Provides pre-built Material Design components
- Customizable theme system
- Built-in accessibility features

### Performance
- Animate using transform and opacity (GPU-accelerated)
- Avoid animating layout properties (width, height, margin)
- Use will-change sparingly for performance hints
- Debounce rapid interactions

## References
- [Material Design Guidelines](https://material.io/design)
- [Material Design Color Tool](https://material.io/resources/color)
- [Material-UI React Components](https://mui.com)
