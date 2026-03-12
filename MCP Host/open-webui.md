# Open WebUI — MCP Config Reference

> 📖 Official docs: [docs.openwebui.com — Model Context Protocol (MCP)](https://docs.openwebui.com/features/extensibility/mcp/)
> 📖 MCPO proxy: [github.com/open-webui/mcpo](https://github.com/open-webui/mcpo)

Open WebUI adalah self-hosted AI platform yang support berbagai LLM runner (Ollama, OpenAI-compatible API). MCP support tersedia, tapi berbeda secara arsitektur dari host lain: **Open WebUI tidak bisa connect langsung ke stdio MCP server**. Ada dua jalur yang perlu dipahami.

## Dua Jalur MCP di Open WebUI

```
Jalur A — Native HTTP (langsung):
Open WebUI ──HTTP──▶ MCP Server (Streamable HTTP endpoint)

Jalur B — via MCPO Proxy (untuk stdio server):
Open WebUI ──OpenAPI──▶ MCPO Proxy ──stdio──▶ MCP Server
```

**Kapan pakai Jalur A:** Server seperti VirusTotal atau Notion yang punya hosted HTTP endpoint.

**Kapan pakai Jalur B:** Server yang berjalan sebagai local process via stdio — mayoritas community MCP server termasuk kategori ini.

## Jalur A — Native HTTP MCP

### Setup

1. Buka **Admin Panel** → **Settings** → **External Tools**
2. Klik **+** (Add Server)
3. Set **Type** ke `MCP (Streamable HTTP)` — jangan pilih OpenAPI, akan crash
4. Masukkan **Server URL** dan credentials
5. **Save**

### Contoh: VirusTotal Hosted MCP

```
Type: MCP (Streamable HTTP)
Server URL: https://mcp.virustotal.com/mcp
Auth: Bearer <your-vt-api-key>
```

### Catatan Penting

- Pastikan `WEBUI_SECRET_KEY` di-set sebagai environment variable yang persistent di Docker setup. Tanpa ini, OAuth token akan invalid setiap kali container restart.
- Jangan pilih "Bearer" auth tanpa mengisi token — ini akan kirim `Authorization: Bearer` dengan value kosong, yang akan ditolak server.

## Jalur B — via MCPO Proxy

MCPO adalah proxy open-source dari tim Open WebUI yang menerjemahkan stdio MCP server menjadi OpenAPI-compatible HTTP endpoint. Ini yang dipakai untuk mayoritas community MCP server.

### Install MCPO

```bash
# Via uvx (paling simpel)
uvx mcpo --port 8000 --api-key "your-secret-key" -- uvx mcp-virustotal

# Via Docker
docker run -d -p 8000:8000 \
  -v mcpo:/app/config \
  --name mcpo \
  ghcr.io/open-webui/mcpo:main \
  mcpo --config /app/config/config.json
```

### Config File MCPO (multi-server)

Format config MCPO identik dengan Claude Desktop `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "virustotal": {
      "command": "uvx",
      "args": ["mcp-virustotal==0.2.1"],
      "env": {
        "VT_API_KEY": "your_vt_key"
      }
    },
    "abuseipdb": {
      "command": "uvx",
      "args": ["mcp-abuseipdb"],
      "env": {
        "ABUSEIPDB_API_KEY": "your_abuseipdb_key"
      }
    },
    "remnux": {
      "command": "docker",
      "args": [
        "run", "--rm", "-i",
        "--network", "none",
        "-v", "/samples:/samples:ro",
        "remnux/mcp-server"
      ]
    }
  }
}
```

Simpan sebagai `config.json`, mount ke MCPO container, restart.

### Tambahkan ke Open WebUI

Setelah MCPO berjalan di port 8000:

1. **Admin Panel** → **Settings** → **External Tools**
2. Klik **+**
3. Set **Type** ke `OpenAPI` (bukan MCP — MCPO expose endpoint OpenAPI)
4. **URL:** `http://localhost:8000`
5. **API Key:** key yang di-set saat jalankan MCPO (`your-secret-key`)
6. **Save**

Tiap tool di MCPO akan tersedia sebagai endpoint terpisah, misalnya:
- `http://localhost:8000/virustotal/`
- `http://localhost:8000/abuseipdb/`

### Docker Compose — Full Setup

```yaml
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    ports:
      - "3000:8080"
    environment:
      - WEBUI_SECRET_KEY=your-persistent-secret-key
      - OLLAMA_BASE_URL=http://ollama:11434
    volumes:
      - open-webui:/app/backend/data
    depends_on:
      - mcpo

  mcpo:
    image: ghcr.io/open-webui/mcpo:main
    ports:
      - "8000:8000"
    volumes:
      - ./mcpo-config.json:/app/config/config.json:ro
    command: mcpo --config /app/config/config.json --api-key "your-mcpo-key"

  ollama:
    image: ollama/ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama:/root/.ollama

volumes:
  open-webui:
  ollama:
```

## Aktifkan Tool di Conversation

Setelah server ditambahkan, tool tidak otomatis aktif di semua conversation. Cara aktifkan:

1. Di chat, klik ikon **➕** di input area
2. Pilih tool yang ingin diaktifkan
3. Atau set sebagai default tool di **Workspace → Models** untuk model tertentu

## ⚠️ Security Considerations

**Eksekusi kode arbitrary**

Open WebUI Native Tools (bukan MCP) adalah Python script yang berjalan langsung di server. Jangan berikan akses Workspace Tools ke user yang tidak trusted — ini equivalent dengan akses untuk run arbitrary code di server.

**API key MCPO**

MCPO mengekspos semua server sebagai HTTP endpoint. Pastikan `--api-key` di-set dan tidak dibiarkan tanpa auth, terutama kalau Open WebUI bisa diakses dari network yang lebih luas.

**`WEBUI_SECRET_KEY` harus persistent**

Set environment variable ini ke nilai yang sama setiap restart. Kalau berubah, semua OAuth token yang tersimpan akan invalid.

```yaml
environment:
  - WEBUI_SECRET_KEY=ganti-dengan-string-random-yang-panjang
```

**Version pinning di MCPO config**

Sama seperti di host lain — pin community server ke versi spesifik di `config.json`:

```json
"args": ["mcp-virustotal==0.2.1"]  // bukan ["mcp-virustotal"]
```

> Full security analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
