# Personal research site

Static site. No build step, no dependencies. Edit `index.html` and push.

## Deploy to GitHub Pages

1. Create a repo named exactly `davidoneill00.github.io` (use your actual GitHub username).
2. From this folder:

```bash
git init
git add .
git commit -m "Initial site"
git branch -M main
git remote add origin https://github.com/davidoneill00/davidoneill00.github.io.git
git push -u origin main
```

3. Repo → Settings → Pages → Source: `main`, folder: `/ (root)`. Save.
4. Live at `https://davidoneill00.github.io` in ~1 minute.

Any push to `main` redeploys automatically.

## Adding figures

Drop files in `assets/img/`, then copy a `<figure>` block in the Gallery section:

```html
<figure>
  <img src="assets/img/my-figure.png" alt="Describe it for screen readers.">
  <figcaption>
    <b>Short title</b>
    One or two sentences of context.
  </figcaption>
</figure>
```

Compress before committing — Git keeps every version forever, so a 5 MB PNG
committed ten times is 50 MB in the repo permanently:

```bash
# resize + compress a figure to ~1600px wide
python3 -c "
from PIL import Image
im = Image.open('raw.png').convert('RGB')
im.thumbnail((1600,1600), Image.LANCZOS)
im.save('assets/img/my-figure.jpg', quality=88, optimize=True, progressive=True)
"
```

## Adding movies

Drop `.mp4` files in `assets/video/`, then:

```html
<figure>
  <video controls muted loop playsinline preload="metadata" poster="assets/img/poster.jpg">
    <source src="assets/video/my-movie.mp4" type="video/mp4">
  </video>
  <figcaption>
    <b>Short title</b>
    What the movie shows.
  </figcaption>
</figure>
```

Convert a simulation frame dump to web-friendly mp4:

```bash
ffmpeg -framerate 30 -i frame_%04d.png \
  -c:v libx264 -pix_fmt yuv420p -crf 23 \
  -vf "scale=1280:-2" -movflags +faststart \
  assets/video/my-movie.mp4
```

`-crf` controls quality: lower is better and bigger (18 = near-lossless, 28 = small).
`-movflags +faststart` lets playback begin before the whole file downloads.

### Size limits — important

GitHub Pages has a **1 GB total repo limit** and **100 MB per file hard limit**.
Keep individual movies under ~50 MB. For anything larger, upload to YouTube
unlisted or Vimeo and embed the iframe instead — don't fight the limit.

## Custom domain (optional)

Buy a domain, add a file named `CNAME` containing just `davidoneill.eu` (or
whatever you bought), then point a CNAME DNS record at `davidoneill00.github.io`.
Settings → Pages → Custom domain to finish, and tick "Enforce HTTPS".

## Still to do

- Add remaining publications (marked with a TODO comment in `index.html`)
- Replace the two placeholder gallery slots with real figures
- Add a headshot to the About section if you want one
- Add `assets/cv.pdf` and link it from the nav
