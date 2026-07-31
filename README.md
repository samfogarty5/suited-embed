# suited-embed

Hero animation for wellsuited.com.

## Usage

Webflow → Home → Page Settings → Custom Code → Before `</body>`:

```html
<script src="https://cdn.jsdelivr.net/gh/TitanHire/suited-embed@v13/embed.js" integrity="sha384-bRcbEkLO/vZZg0c5ak4h3Fr8sh9nq7Nn58m2VcPuYMbGFxuuVabnNTRbBt8o1JAo" crossorigin="anonymous"></script>
```

Always pin to a tag, never `@main`, and keep the `integrity` hash.

To ship a change: push a new tag, then recompute the hash and update both in Webflow.

```
shasum -b -a 384 embed.js | cut -d' ' -f1 | xxd -r -p | base64
```
