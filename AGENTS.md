# AGENTS.md - Flet Framework Guide for AI Assistants

## Overview

Flet is a Python framework for building cross-platform applications (web, desktop, and mobile) without requiring frontend development experience. It uses Flutter for UI rendering and supports both imperative and declarative programming styles.

## Key Characteristics

### Framework Identity
- **Language**: Python-only (no Dart, Swift, Kotlin, HTML, or JavaScript required)
- **UI Framework**: Flutter-based (Material and Cupertino design)
- **Platforms**: Web (WASM/Pyodide or server-side), Desktop (Windows, Linux, macOS), Mobile (iOS, Android)
- **Architecture**: Single monolith stateful apps with real-time UI updates
- **Version**: Flet 1.0+ represents a complete re-architecture from earlier versions

### Programming Paradigms

Flet supports two distinct approaches:

1. **Imperative Style** (traditional):
```python
import flet as ft

def main(page: ft.Page):
    counter = ft.Text(value="0")
    
    def increment(e):
        counter.value = str(int(counter.value) + 1)
        page.update()
    
    page.add(
        counter,
        ft.Button("Add", on_click=increment)
    )

ft.app(target=main)
```

2. **Declarative Style** (Flet 1.0+):
```python
import flet as ft

@ft.component
def App():
    count, set_count = ft.use_state(0)
    
    return ft.Row(
        controls=[
            ft.Text(value=f"{count}"),
            ft.Button("Add", on_click=lambda: set_count(count + 1)),
        ],
    )

ft.run(lambda page: page.render(App))
```

## Best Practices for AI Assistants

### 1. Version Awareness
- Always check which Flet version the user is working with (0.x vs 1.0+)
- Flet 1.0 introduced breaking changes and new features
- Extensions for Flet v1 use version 0.2.x+, while Flet v0 extensions use 0.1.x

### 2. Code Generation Guidelines

#### Controls vs Widgets
- Use the term "controls" (not Flutter "widgets")
- Controls are higher-level abstractions that combine multiple Flutter widgets
- All controls are implemented as Python dataclasses in Flet 1.0+

#### Common Controls to Use
- Layout: `Row`, `Column`, `Container`, `Stack`, `GridView`, `ListView`
- Navigation: `NavigationRail`, `NavigationBar`, `AppBar`, `Tabs`
- Input: `TextField`, `Dropdown`, `Checkbox`, `Switch`, `Slider`, `DatePicker`
- Display: `Text`, `Image`, `Icon`, `ProgressBar`, `Chart`
- Buttons: `ElevatedButton`, `TextButton`, `IconButton`, `FloatingActionButton`
- Dialogs: `AlertDialog`, `BottomSheet`, `Snackbar`

#### State Management
- For imperative style: Use `page.update()` after state changes
- For declarative style: Use hooks like `ft.use_state()`, `ft.use_effect()`, `ft.use_memo()`
- Services: Use for persistent, non-UI components (Audio, FilePicker, Clipboard)

#### Auto-Update Feature (Flet 1.0+)
- Pages automatically update after event handler completion
- Reduces need for manual `page.update()` calls in many cases

### 3. Project Structure

#### Single-File Apps
```
my_app.py
```

#### Multi-Module Apps
```
my_app/
├── main.py
├── views/
│   ├── home_view.py
│   └── settings_view.py
├── components/
│   └── custom_button.py
└── services/
    └── data_service.py
```

### 4. Deployment Patterns

When helping users deploy Flet apps, consider:

- **Web (WASM)**: Client-side only, fast, offline-capable
  ```bash
  flet build web
  ```

- **Web (Server-side)**: Python backend, real-time updates
  ```bash
  flet run --web
  ```

- **Desktop**: Standalone executables
  ```bash
  flet build windows  # or macos, linux
  ```

- **Mobile**: App bundles for iOS/Android
  ```bash
  flet build apk     # or ipa
  ```

### 5. Common Patterns

#### Routing/Navigation
```python
def main(page: ft.Page):
    def route_change(e):
        page.views.clear()
        page.views.append(
            ft.View(
                "/",
                [ft.AppBar(title=ft.Text("Home")), ft.Text("Home Page")]
            )
        )
        if page.route == "/settings":
            page.views.append(
                ft.View(
                    "/settings",
                    [ft.AppBar(title=ft.Text("Settings")), ft.Text("Settings Page")]
                )
            )
        page.update()
    
    page.on_route_change = route_change
    page.go("/")
```

#### Responsive Design
```python
def main(page: ft.Page):
    def page_resize(e):
        # Adapt layout based on page width
        if page.width < 600:
            # Mobile layout
            pass
        else:
            # Desktop layout
            pass
    
    page.on_resize = page_resize
```

#### Services Usage (Flet 1.0+)
```python
import flet as ft

def main(page: ft.Page):
    # Services are persistent across rebuilds
    audio = ft.Audio(src="music.mp3")
    page.overlay.append(audio)
    
    file_picker = ft.FilePicker(on_result=handle_file)
    page.overlay.append(file_picker)
```

### 6. Styling and Theming

```python
page.theme = ft.Theme(
    color_scheme_seed=ft.colors.INDIGO,
    visual_density=ft.ThemeVisualDensity.COMPACT,
)

page.theme_mode = ft.ThemeMode.DARK  # or LIGHT, SYSTEM
```

### 7. Common Libraries Integration

Flet works with popular Python libraries:
- **Data**: numpy, pandas, pydantic
- **Imaging**: opencv, pillow
- **Encryption**: cryptography
- **And many more...**

### 8. Testing

Flet 1.0+ includes a testing framework:
```python
import flet as ft
from flet.testing import TestCase

class MyAppTests(TestCase):
    def test_button_click(self):
        # Test implementation
        pass
```

### 9. Extensions

When working with extensions:
- Audio: `flet-audio` package
- Video: `flet-video` package
- Ads: Built into main Flet repo (v1+)
- Custom extensions can expose both controls and services

### 10. Debugging

```bash
# Debug on real devices/emulators
flet debug

# Run with hot reload
flet run --web
```

## Common Mistakes to Avoid

1. **Don't mix Flutter terminology**: Use "controls" not "widgets"
2. **Don't forget page.update()** in imperative style (unless auto-update applies)
3. **Don't use async/await for event handlers** unless specifically needed
4. **Don't forget to add services to page.overlay**
5. **Don't assume frontend knowledge is required** - Flet abstracts Flutter complexity

## Version-Specific Guidance

### Flet 0.x (Legacy)
- Primarily imperative approach
- Redux-based state management
- Extensions in separate repos
- Manual documentation

### Flet 1.0+ (Current)
- Both imperative and declarative approaches
- InheritedWidget + Provider state management
- Auto-generated documentation from docstrings
- Integrated extensions
- WASM support with offline mode
- Enhanced testing framework
- Strongly-typed event handlers

## Resources to Reference

- Official docs: https://flet.dev/docs/
- GitHub repo: https://github.com/flet-dev/flet
- PyPI package: https://pypi.org/project/flet/
- Examples: https://github.com/flet-dev/flet/tree/main/examples
- Tutorials: https://flet.dev/docs/tutorials/

## When Helping Users

1. **Ask about their target platform** (web, desktop, mobile, or all)
2. **Determine if they prefer imperative or declarative style**
3. **Check their Flet version** (`pip show flet`)
4. **Consider their Python skill level** (Flet is beginner-friendly)
5. **Suggest appropriate controls** based on Material/Cupertino design patterns
6. **Provide complete, runnable examples** when possible
7. **Explain deployment options** based on their needs

## Example Use Cases

- **Real-time dashboards**: Leverage server-side mode with WebSockets
- **Desktop utilities**: Build standalone executables
- **Mobile apps**: Deploy to App Store/Google Play
- **Internal tools**: Quick CRUD interfaces with database integration
- **Data visualization**: Combine with matplotlib, plotly, or built-in charts
- **Admin panels**: Rapid prototyping with rich UI components

## Architecture Considerations

- **Client-side WASM**: Best for static apps, no server costs, offline support
- **Server-side**: Best for dynamic data, database access, multi-user collaboration
- **Hybrid**: Possible to mix approaches for different app sections

## Performance Tips

1. Use `page.update()` efficiently - batch updates when possible
2. Leverage lazy loading for large lists with `ListView`
3. Optimize images before including them
4. Use services for expensive operations that shouldn't re-initialize
5. Profile with Python profiling tools if needed

---

**Remember**: Flet's core philosophy is making cross-platform app development accessible to Python developers without requiring frontend expertise. Keep solutions Pythonic and straightforward.
