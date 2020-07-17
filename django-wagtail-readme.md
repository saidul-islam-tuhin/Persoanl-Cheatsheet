# MultiFieldPanel
### Exampla:
```python
    banner_title = models.CharField(max_length=150, blank=False, null=True)
    banner_subtitle = RichTextField(blank=True, null=True, features=["bold", "italic"])
    banner_background_image = models.ForeignKey(
        "wagtailimages.Image",
        null=True,
        blank=False,
        on_delete=models.SET_NULL,
        related_name="+"
    )

    content_panels = Page.content_panels + [
        MultiFieldPanel(
            [
                FieldPanel('banner_title', classname="title" ),
                FieldPanel('banner_subtitle' ),
                ImageChooserPanel('banner_background_image'),
            ],
            heading = "Banner",
            classname = "collapsible collapsed"
        )
    ]
```
### ScreenShot:
