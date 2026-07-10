We used the instructinos below to add a hyperlink to `Kantian x III`. Follow this script for further hyperlinks. But there must be a more officient `CLI automation` approach e.g.:

```sh
#!/bin/bash

echo "Initiating global theme variable migration..."

TARGET_DIR="frontend/src/apps"

# Ensure the directory exists
if [ ! -d "$TARGET_DIR" ]; then
  echo "Error: Directory $TARGET_DIR does not exist."
  exit 1
fi

# Execute inline replacements across all JSX files
# This maps your old static variables to the new semantic system
find "$TARGET_DIR" -name "*.jsx" -type f -exec sed -i '' \
  -e 's/background: "var(--cream)"/background: "transparent"/g' \
  -e 's/background: "var(--white)"/background: "var(--bg-raised)"/g' \
  -e 's/color: "var(--charcoal)"/color: "var(--text)"/g' \
  -e 's/color: "var(--slate)"/color: "var(--text-sub)"/g' \
  -e 's/color: "var(--mist)"/color: "var(--text-muted)"/g' \
  -e 's/var(--green-pale)/var(--bg-sunken)/g' \
  -e 's/var(--green-light)/var(--accent-glow)/g' \
  -e 's/var(--green-bright)/var(--accent)/g' \
  -e 's/var(--green-mid)/var(--accent)/g' \
  -e 's/var(--gold)/var(--accent)/g' \
  -e 's/var(--gold-light)/var(--accent-dim)/g' \
  -e 's/background: "var(--green-deep)", color: "var(--white)"/background: "var(--ink)", color: "#ffffff"/g' \
  {} +

echo "Migration complete. UI layers have been re-mapped to semantic variables."
```

Since I'm no coder, this human-free, automated, CLI approach needs must become the standard way we do things around here

---


Because your table’s search script uses `textContent` and a global regex replacement to highlight search strings, dropping a standard `<a href="...">` tag straight into the HTML will break the DOM the moment you filter for any letter that happens to be inside the URL (like "t" or "p").

To keep the addition tasteful and preserve the integrity of your filtering logic, the cleanest approach is to turn the `<td>` itself into a clickable trigger via a data attribute. We can style it with a subtle dotted underline that resolves to a solid amber on hover, honoring your existing design language.

Here are the three minor adjustments to make to your file:

### 1. The HTML (The Grid)

Locate the Kantian row in your `<tbody>` and update the `III` column to include the `cell-link` class and the `data-href` attribute.

```html
<tr data-domain="Kantian">
  <th scope="row" tabindex="0" role="button"><span class="letter">K</span>Kantian</th>
  <td data-col="1">Dionysian</td>
  <td data-col="2">Apollonian</td>
  <td data-col="3" class="cell-link" data-href="https://ukb-dt.github.io/attempt-at-self-criticism/" title="An Attempt at Self-Criticism">Tragicomic</td>
  <td data-col="4">Eternal Recurrence</td>
  <td data-col="5">Will-to-Power</td>
</tr>

```

### 2. The CSS (The Styling)

Drop this into your `<style>` block (right around where you define `tbody td` or `td.col-focused`):

```css
  td.cell-link {
    cursor: pointer;
    text-decoration: underline;
    text-decoration-style: dotted;
    text-decoration-color: var(--text-faint);
    text-underline-offset: 4px;
  }
  td.cell-link:hover {
    color: var(--amber);
    text-decoration-color: var(--amber);
    text-decoration-style: solid;
  }

```

### 3. The JavaScript (The Event)

Add this small block inside your `<script>` tag, right before the `// Theme toggle` section. This ensures the click routes correctly without interfering with your row-pinning or column-focusing logic.

```javascript
  // Cell Links Handling
  Array.prototype.slice.call(table.querySelectorAll('td.cell-link')).forEach(function(cell) {
    cell.addEventListener('click', function(e) {
      e.stopPropagation(); // Prevents grid-focus events from consuming the click
      window.open(this.dataset.href, '_blank', 'noopener,noreferrer');
    });
  });

```