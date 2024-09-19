### Add favicon

```python
app_ui = ui.page_fluid(
    
    ui.head_content(
        ui.tags.link(rel="shortcut icon", type="image/png", href="favicon.png")
    ),
    ...
)

path_static = os.path.join(os.path.dirname(__file__), "static")
app = App(app_ui, server, static_assets={"/": path_static})

if __name__ == "__main__":
    app.run()
```
