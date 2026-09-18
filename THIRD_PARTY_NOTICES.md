# Third-party notices

`index.html` is self-contained apart from the items below. One of them, the embedded font, carries an attribution requirement that travels with any copy of this repository or of the page — keep this file and `licenses/` alongside it.

---

## DejaVu Sans — **embedded**, attribution required

A subset of DejaVu Sans (regular and bold, reduced to ASCII, Greek and a few mathematical signs) is embedded in `index.html` as base64 and handed to jsPDF, so that the PDF report can print Greek symbols and real subscripts. jsPDF's built-in Helvetica is WinAnsi-encoded and carries no Greek at all.

- Upstream: https://dejavu-fonts.github.io/
- Licence: Bitstream Vera Fonts Copyright, with the DejaVu and Arev additions — full text in [`licenses/LICENSE_DEJAVU.txt`](licenses/LICENSE_DEJAVU.txt)
- Obligations: the copyright notice and permission notice must be included with any distribution of the Font Software. The licence also forbids selling the font on its own, and restricts use of the "Bitstream Vera" and "Tavmjong Bah Arev" names. Neither restriction affects normal use or redistribution of this repository.

This is the only third-party component that ships inside the file.

---

## jsPDF — loaded at runtime from a CDN

Used to generate the PDF specimen record. Loaded from cdnjs at an exact pinned version; not redistributed here.

- Upstream: https://github.com/parallax/jsPDF
- Licence: MIT
- Loaded from: `https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js`

Because it is fetched at runtime, the PDF button needs network access on first use. Everything else in the page works offline.

---

## IBM Plex Sans, IBM Plex Sans Condensed, IBM Plex Mono — loaded at runtime

The on-screen typefaces. Requested from Google Fonts; not redistributed here.

- Upstream: https://github.com/IBM/plex
- Licence: SIL Open Font License 1.1
- Loaded from: `https://fonts.googleapis.com`

If the request fails, the page falls back to the system sans-serif and monospace stacks declared in the stylesheet, and remains fully usable.

---

## A note on privacy

The page makes two outbound requests, both for the assets above: Google Fonts on load, and cdnjs the first time the PDF button is used. No specimen data leaves the browser. Records are kept in that browser's local storage.

If you need the page to make no outbound requests at all, remove the `<link>` to Google Fonts and the `<script>` tag for jsPDF from `index.html`. The typography falls back to system fonts and the PDF button reports that the library did not load; everything else, including CSV export, is unaffected.
