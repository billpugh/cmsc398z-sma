# Shared Static Assets

This directory contains static assets shared between Phase 1 and Phase 2.

## Files

### `style.css` (Created in Phase 1, Part 1)
- Custom CSS with your chosen color scheme
- CSS variables for colors, typography, spacing
- Component styles (buttons, cards, forms, navbar)
- Responsive layout utilities
- **Used by both Phase 1 Flask templates AND Phase 2 frontend**

## Usage

### Phase 1 (Flask)
In your Flask templates, reference the shared stylesheet:
```html
<link rel="stylesheet" href="{{ url_for('static', filename='../../shared/static/style.css') }}">
```

Or copy to `phase1/static/` and reference normally:
```html
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
```

### Phase 2 (React/Vue)
Import in your main component or index.html:
```javascript
// In React component
import '../../shared/static/style.css';
```

Or reference in `index.html`:
```html
<link rel="stylesheet" href="/shared/static/style.css">
```

## Benefits

- **Consistency**: Same visual design across both architectures
- **DRY Principle**: Define colors once, use everywhere
- **Easy updates**: Change one file to update both phases
- **Portfolio-ready**: Cohesive visual identity
