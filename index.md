# This is a H1 Header
## H2 Header
### H3 Header
#### H4 Header
##### No points for guessing what this is called
###### Why are you nesting headers this deep?

![UTD Logo](https://sandbox.utdallas.edu/brand/files/2022/04/monogram-emblem-border-880-1.png)


Cool program that also updates macos dock icon.
Find it ![here](https://github.com/LandryNorris/ComposeMacosDock)

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.BoxScope
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.runtime.Composable
import androidx.compose.runtime.LaunchedEffect
import androidx.compose.runtime.remember
import androidx.compose.runtime.withFrameMillis
import androidx.compose.ui.Alignment
import androidx.compose.ui.ImageComposeScene
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import kotlinx.cinterop.addressOf
import kotlinx.cinterop.usePinned
import platform.AppKit.NSApp
import platform.AppKit.NSImage
import platform.AppKit.NSImageView
import platform.Foundation.NSData
import platform.Foundation.dataWithBytes

internal class DockController(w: Int, h: Int, content: @Composable BoxScope.() -> Unit = {}) {
    private val scene = ImageComposeScene(w, h, content = createContent(content))
    private val imageView = NSImageView()
    private var running = true

    internal fun setContent(content: @Composable BoxScope.() -> Unit) {
        scene.setContent(createContent(content))
    }

    private fun createContent(content: @Composable BoxScope.() -> Unit): @Composable () -> Unit = {
        Box(modifier = Modifier
            .fillMaxSize()
            .clip(RoundedCornerShape(20.dp))
            .background(Color.White),
            contentAlignment = Alignment.Center, content = content)
    }

    @Composable
    fun runEffect() {
        LaunchedEffect(Unit) {
            while(running) {
                withFrameMillis {
                    if(scene.hasInvalidations()) display()
                }
            }
        }
    }

    fun stop() {
        running = false
    }

    private fun display() {
        val bytes = scene.render().encodeToData()?.bytes ?: error("Unable to get Data from scene")

        val data = bytes.usePinned {
            NSData.dataWithBytes(it.addressOf(0), bytes.size.toULong())
        }

        imageView.image = NSImage(data = data)

        NSApp?.dockTile?.setContentView(imageView)
        NSApp?.dockTile?.display()
    }
}

@Composable
internal fun Dock(w: Int, h: Int, content: @Composable BoxScope.() -> Unit) {
    val controller = remember(w, h, content) { DockController(w, h, content) }
    controller.runEffect()
}
```

Task List

- [x] start CS 3354
- [ ] finish CS 3354
- [ ] finish Senior design
- [ ] graduate college
