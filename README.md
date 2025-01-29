<img src="dot_morph.gif" style="display: block;margin-left: auto;margin-right: auto;">
<details>
<summary></summary>

```python
import numpy as np
from PIL import Image, ImageDraw

r, speed, fps = 20, 40, 24
x1, y1, x2, y2, frames, c = 0.0, 0.0, -0.0, 0.0, [], 0
duration, total_steps, t_increment, t = int(1000 / fps), 640 - r // r, 1 / (640 - r // r), 0

curve_multi = lambda t: t ** 2 / (4 * (t ** 2 - t) + 1)
equation_lhs = lambda x, y, r, x1, y1, x2, y2: r / np.sqrt((x - x1) ** 2 + (y - y1) ** 2) + r / np.sqrt((x - x2) ** 2 + (y - y2) ** 2)

while x1 < 640 - r * 1.5 and x2 > -640 + r * 1.5:
    c += 1
    X, Y = np.meshgrid(np.linspace(-640, 640, 1280), np.linspace(-64, 64, 128))
    Z = equation_lhs(X, Y, r, x1, y1, x2, y2)
    image = Image.new("RGBA", (1280, 128), (0, 0, 0, 0))
    draw = ImageDraw.Draw(image)
    for i in range(1280):
        for j in range(128):
            if Z[j, i] >= 1: draw.point((i, j), fill='white')
    t += t_increment
    x1 += speed * curve_multi(t)
    x2 -= speed * curve_multi(t)
    frames.append(image)

while x1 > 0 and x2 < 0:
    c += 1
    X, Y = np.meshgrid(np.linspace(-640, 640, 1280), np.linspace(-64, 64, 128))
    Z = equation_lhs(X, Y, r, x1, y1, x2, y2)
    image = Image.new("RGBA", (1280, 128), (0, 0, 0, 0))
    draw = ImageDraw.Draw(image)
    for i in range(1280):
        for j in range(128):
            if Z[j, i] >= 1: draw.point((i, j), fill='white')
    t -= t_increment
    x1 -= speed * curve_multi(t)
    x2 += speed * curve_multi(t)
    frames.append(image)

frames[0].save('dot_morph.gif', save_all=True, append_images=frames[1:], duration=duration, loop=0, disposal=2, transparency=0)
```
</details>
