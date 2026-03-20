# UV — package manager dla Pythona

UV to ultra-szybki package manager i narzędzie do zarządzania projektami Python, napisany w Rust przez firmę Astral (twórców lintera Ruff). Zastępuje pip, pip-tools, virtualenv, pyenv i poetry jednym narzędziem.

## Główne zalety

- **Szybkość** — instalacja paczek nawet 10-100x szybsza niż pip
- **Wszystko w jednym** — zarządzanie wersjami Pythona, zależnościami, środowiskami wirtualnymi i uruchamianiem skryptów
- **Kompatybilność** — działa z istniejącym ekosystemem (pyproject.toml, requirements.txt)
- **Deterministyczny lockfile** — uv.lock gwarantuje powtarzalne buildy

## Instalacja

```bash
# macOS/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# lub przez Homebrew
brew install uv
```

## Zarządzanie projektem

```bash
# Nowy projekt
uv init my-project
cd my-project

# Dodanie zależności
uv add pandas matplotlib numpy
uv add --dev pytest ruff          # dev dependencies

# Usunięcie zależności
uv remove pandas

# Synchronizacja (instalacja wg pyproject.toml / uv.lock)
uv sync

# Uruchomienie skryptu w środowisku projektu
uv run python main.py
uv run pytest tests/ -v
```

## Zarządzanie Pythonem

```bash
# Instalacja konkretnej wersji Pythona
uv python install 3.12

# Lista dostępnych wersji
uv python list

# Przypięcie wersji do projektu (tworzy .python-version)
uv python pin 3.12
```

## Kluczowe pliki

| Plik              | Rola                             |
|-------------------|----------------------------------|
| `pyproject.toml`  | Definicja projektu i zależności  |
| `uv.lock`         | Lockfile (commituj do repo)      |
| `.python-version` | Przypięta wersja Pythona         |
| `.venv/`          | Wirtualne środowisko (gitignore) |

## Jednorazowe narzędzia (bez instalacji)

```bash
# Odpowiednik npx — uruchom narzędzie bez instalacji
uvx ruff check .
uvx black .
uvx mypy src/
```

## Integracja z VSCode

1. **Wybór interpretera** — po `uv sync` VSCode powinien automatycznie wykryć `.venv`. Jeśli nie:
   - `Cmd+Shift+P` → "Python: Select Interpreter"
   - Wybierz `./.venv/bin/python`

2. **Ustawienia workspace** — dodaj do `.vscode/settings.json`:
   ```json
   {
     "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python",
     "python.terminal.activateEnvironment": true
   }
   ```

3. **Terminal** — VSCode automatycznie aktywuje `.venv` w nowym terminalu. Jeśli nie, `uv run` zawsze użyje właściwego środowiska.

## Jupyter Notebooks w VSCode

### Konfiguracja

```bash
# Dodaj ipykernel jako dev dependency
uv add --dev ipykernel
```

### Wymagane rozszerzenia VSCode

- **Jupyter** (`ms-toolsai.jupyter`) — obsługa notebooków
- **Python** (`ms-python.python`) — interpreter i IntelliSense

### Praca z notebookami

1. `Cmd+Shift+P` → "Create: New Jupyter Notebook" (lub utwórz plik `.ipynb`)
2. Kliknij "Select Kernel" (prawy górny róg) → "Python Environments" → wybierz `.venv` z projektu
3. Gotowe — notebook korzysta z tych samych zależności co projekt

> Jeśli kernel nie widzi nowych paczek po `uv add`, kliknij restart kernel (`Cmd+Shift+P` → "Jupyter: Restart Kernel").

## Przydatne komendy

```bash
uv tree              # Drzewo zależności
uv pip list          # Lista zainstalowanych pakietów
uv lock --upgrade    # Aktualizacja lockfile'a do najnowszych wersji
uv self update       # Aktualizacja samego uv
```

## Migracja z pip/poetry

```bash
# Z istniejącego requirements.txt
uv add $(cat requirements.txt)

# Z poetry — uv czyta pyproject.toml, wystarczy:
uv sync
```

---

> **Najważniejsza zasada:** zawsze używaj `uv run` zamiast bezpośredniego `python` — to gwarantuje, że skrypt działa w poprawnym środowisku z właściwymi zależnościami.
