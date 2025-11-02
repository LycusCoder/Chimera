# 🎨 UI/UX Design Guidelines

## 🎯 Design Philosophy

**Modern, Clean, Intuitive**

Laragon Cross-Platform harus memiliki UI yang:
1. **Modern** - Mengikuti design trends terbaru
2. **Clean** - Minimalis, tidak overwhelming
3. **Intuitive** - Mudah digunakan tanpa tutorial
4. **Fast** - Responsive dan smooth
5. **Accessible** - Mudah diakses oleh semua user

---

## 🎨 Color Scheme

### Light Mode (Default)

**Primary Colors:**
```css
--primary: #3B82F6        /* Blue 500 */
--primary-hover: #2563EB  /* Blue 600 */
--primary-light: #DBEAFE /* Blue 50 */
```

**Status Colors:**
```css
--success: #10B981        /* Green 500 */
--warning: #F59E0B        /* Amber 500 */
--error: #EF4444          /* Red 500 */
--info: #06B6D4           /* Cyan 500 */
```

**Neutral Colors:**
```css
--background: #FFFFFF     /* White */
--surface: #F9FAFB        /* Gray 50 */
--border: #E5E7EB         /* Gray 200 */
--text: #111827           /* Gray 900 */
--text-muted: #6B7280     /* Gray 500 */
```

### Dark Mode

**Primary Colors:**
```css
--primary: #3B82F6        /* Blue 500 */
--primary-hover: #60A5FA  /* Blue 400 */
--primary-light: #1E3A8A  /* Blue 900 */
```

**Neutral Colors:**
```css
--background: #0F172A     /* Slate 900 */
--surface: #1E293B        /* Slate 800 */
--border: #334155         /* Slate 700 */
--text: #F1F5F9           /* Slate 100 */
--text-muted: #94A3B8     /* Slate 400 */
```

---

## 📝 Typography

**Font Family:**
```css
font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', 
             'Roboto', 'Oxygen', 'Ubuntu', 'Cantarell', sans-serif;
```

**Font Sizes:**
```css
--text-xs: 0.75rem;      /* 12px */
--text-sm: 0.875rem;     /* 14px */
--text-base: 1rem;       /* 16px */
--text-lg: 1.125rem;     /* 18px */
--text-xl: 1.25rem;      /* 20px */
--text-2xl: 1.5rem;      /* 24px */
--text-3xl: 1.875rem;    /* 30px */
--text-4xl: 2.25rem;     /* 36px */
```

**Font Weights:**
```css
--font-normal: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;
```

---

## 📱 Layout Structure

### Main Window Layout

```
┌──────────────────────────────────────────────────┐
│                  Title Bar                           │
├──────────────────────────────────────────────────┤
│                                                  │
│  ┌───────────┐  ┌───────────────────────────┐  │
│  │           │  │                           │  │
│  │           │  │                           │  │
│  │  Sidebar  │  │      Main Content        │  │
│  │           │  │                           │  │
│  │           │  │                           │  │
│  │           │  │                           │  │
│  │           │  │                           │  │
│  └───────────┘  └───────────────────────────┘  │
│                                                  │
└──────────────────────────────────────────────────┘
```

**Dimensions:**
- Window: 1200x800px (minimum)
- Sidebar: 240px width
- Main Content: Flexible
- Padding: 16px-24px

---

## 🧱 Components

### 1. Sidebar Navigation

**Structure:**
```
┌────────────────────┐
│  🏠 Dashboard     │  ← Active
├────────────────────┤
│  🛠️ Services       │
├────────────────────┤
│  🌐 Virtual Hosts │
├────────────────────┤
│  📁 Projects       │
├────────────────────┤
│  ⌨️ Terminal       │
├────────────────────┤
│  ⚙️ Settings       │
└────────────────────┘
```

**Features:**
- Icon + Label
- Active state indicator (primary color background)
- Hover effect
- Smooth transitions

---

### 2. Dashboard

**Layout:**
```
┌────────────────────────────────────────────┐
│  Dashboard                                    │
├────────────────────────────────────────────┤
│                                              │
│  ┌──────────────┐   ┌──────────────┐  │
│  │  Service Status │   │  Quick Actions │  │
│  │                │   │                │  │
│  │  Apache: 🟢     │   │  [Start All]    │  │
│  │  Nginx:  🔴     │   │  [Stop All]     │  │
│  │  MySQL:  🟢     │   │  [Open Terminal]│  │
│  └──────────────┘   └──────────────┘  │
│                                              │
│  ┌────────────────────────────────┐  │
│  │  Recent Projects              │  │
│  │                                  │  │
│  │  📁 myproject.test             │  │
│  │  📁 laravel-app.test           │  │
│  │  📁 wordpress.test             │  │
│  └────────────────────────────────┘  │
│                                              │
└────────────────────────────────────────────┘
```

**Components:**
- Service status cards
- Quick action buttons
- Recent projects list
- System info (optional)

---

### 3. Services Page

**Layout:**
```
┌────────────────────────────────────────────┐
│  Services                                     │
├────────────────────────────────────────────┤
│                                              │
│  ┌────────────────────────────────────┐  │
│  │ Apache                  🟢 Running    │  │
│  │ Port: 80, 443                         │  │
│  │ [Start] [Stop] [Restart] [Config]    │  │
│  └────────────────────────────────────┘  │
│                                              │
│  ┌────────────────────────────────────┐  │
│  │ MySQL                   🟢 Running    │  │
│  │ Port: 3306                            │  │
│  │ [Start] [Stop] [Restart] [Config]    │  │
│  └────────────────────────────────────┘  │
│                                              │
└────────────────────────────────────────────┘
```

**Service Card Components:**
- Service name + icon
- Status indicator (green/red dot)
- Port information
- Action buttons (Start, Stop, Restart, Config)
- Optional: CPU/Memory usage

---

### 4. Virtual Hosts Page

```
┌────────────────────────────────────────────┐
│  Virtual Hosts                [+ New]          │
├────────────────────────────────────────────┤
│                                              │
│  ┌────────────────────────────────────┐  │
│  │ 🌐 myproject.test                  │  │
│  │    /www/myproject                    │  │
│  │    [Open] [Edit] [Delete]            │  │
│  └────────────────────────────────────┘  │
│                                              │
│  ┌────────────────────────────────────┐  │
│  │ 🌐 laravel-app.test                │  │
│  │    /www/laravel-app                  │  │
│  │    [Open] [Edit] [Delete]            │  │
│  └────────────────────────────────────┘  │
│                                              │
└────────────────────────────────────────────┘
```

**VHost Card Components:**
- Domain name
- Project path
- Action buttons (Open in browser, Edit config, Delete)
- Optional: SSL indicator

---

## 🎭 System Tray

**Tray Menu:**
```
┌──────────────────────────────┐
│  Laragon 🟢                 │
├──────────────────────────────┤
│  ▶️ Start All             │
│  ⏸️ Stop All              │
├──────────────────────────────┤
│  Projects ►                │
│    • myproject.test       │
│    • laravel-app.test    │
├──────────────────────────────┤
│  💻 Terminal             │
│  ⚙️ Settings              │
├──────────────────────────────┤
│  Show/Hide Window         │
│  Quit                     │
└──────────────────────────────┘
```

**Tray Icons:**
- 🟢 Running (green)
- 🔴 Stopped (red)
- 🟡 Partial (yellow - some services running)

---

## 🎬 Animations & Transitions

### Page Transitions
```css
/* Fade in */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Slide in from right */
@keyframes slideInRight {
  from { 
    transform: translateX(20px);
    opacity: 0;
  }
  to { 
    transform: translateX(0);
    opacity: 1;
  }
}
```

### Button Hover
```css
button {
  transition: all 0.2s ease-in-out;
}

button:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
```

### Status Changes
```css
/* Pulse effect for status change */
@keyframes pulse {
  0% { opacity: 1; }
  50% { opacity: 0.5; }
  100% { opacity: 1; }
}
```

---

## ♿ Accessibility

### 1. Keyboard Navigation
- Tab through all interactive elements
- Enter/Space to activate buttons
- Esc to close modals
- Arrow keys for navigation

### 2. Screen Reader Support
- Semantic HTML
- ARIA labels
- Alt text for images/icons
- Focus indicators

### 3. Color Contrast
- WCAG AA compliance
- Minimum 4.5:1 contrast ratio for text
- Status not indicated by color alone

---

## 📱 Responsive Design

**Breakpoints:**
```css
/* Minimum window size */
@media (min-width: 800px) { ... }

/* Normal desktop */
@media (min-width: 1200px) { ... }

/* Large screens */
@media (min-width: 1600px) { ... }
```

**Adaptive Layout:**
- Sidebar collapses on smaller windows
- Cards stack vertically on narrow windows
- Text sizes adjust proportionally

---

## 🔔 Notifications

**Toast Notifications:**
```
┌──────────────────────────────┐
│  ✅ Apache started successfully  │
└──────────────────────────────┘
```

**Types:**
- Success (green)
- Error (red)
- Warning (yellow)
- Info (blue)

**Position:** Top-right corner

**Duration:** 3-5 seconds (auto-dismiss)

---

## 🎮 Interactive States

### Button States
```css
/* Default */
background: primary;

/* Hover */
background: primary-hover;
transform: translateY(-1px);

/* Active/Pressed */
background: primary-dark;
transform: translateY(0);

/* Disabled */
background: gray;
opacity: 0.5;
cursor: not-allowed;

/* Loading */
opacity: 0.7;
cursor: wait;
/* Show spinner */
```

### Card States
```css
/* Default */
border: 1px solid border;

/* Hover */
border: 1px solid primary;
box-shadow: 0 4px 12px rgba(0,0,0,0.1);

/* Active/Selected */
background: primary-light;
border: 2px solid primary;
```

---

**Design System:** Tailwind CSS 3+

**Icons:** Lucide React (modern, customizable)

**Font:** Inter (Google Fonts)

---

**Last Updated:** Phase 0 - UI/UX Design Planning