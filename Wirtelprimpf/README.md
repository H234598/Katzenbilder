# Wirtelprimpf

Dieser Ordner wird automatisch vom lokalen Wirtelprimpf-Generator auf
`/home/teladi` befuellt.

## Ablauf

- `systemd --user` startet `wirtelprimpf.timer` einmal pro Stunde.
- Der Timer startet `wirtelprimpf.service`.
- Der Service liest `~/.config/wirtelprimpf/openai.env`.
- Das Python-Skript erzeugt ein neues Bild mit der OpenAI Images API.
- Das Bild und der verwendete Prompt werden lokal unter
  `/home/teladi/Hintergrundbilder` gespeichert.
- Dieselben zwei Dateien werden in diesen Repo-Ordner `Wirtelprimpf/` kopiert.
- Danach committet und pusht das Skript die neuen Dateien nach GitHub.

## Lokale Dateien

```text
/home/teladi/.local/bin/wirtelprimpf-generate.py
/home/teladi/.local/bin/wirtelprimpf-set-openai-key
/home/teladi/.config/wirtelprimpf/openai.env
/home/teladi/.config/systemd/user/wirtelprimpf.service
/home/teladi/.config/systemd/user/wirtelprimpf.timer
/home/teladi/.local/share/wirtelprimpf/github/Katzenbilder
```

## Zielordner

```text
Lokal:  /home/teladi/Hintergrundbilder
GitHub: Wirtelprimpf/
```

## API-Key

Der OpenAI API-Key liegt nicht im Repository. Er wird lokal in
`~/.config/wirtelprimpf/openai.env` gespeichert. Die Datei ist mit `0600`
geschuetzt und wird vom User-Service als `EnvironmentFile` gelesen.

Key neu setzen:

```bash
~/.local/bin/wirtelprimpf-set-openai-key
```

## Manuell Starten

```bash
systemctl --user start wirtelprimpf.service
```

Logs ansehen:

```bash
journalctl --user -u wirtelprimpf.service -n 100 --no-pager
```

Timer pruefen:

```bash
systemctl --user status wirtelprimpf.timer --no-pager
```
