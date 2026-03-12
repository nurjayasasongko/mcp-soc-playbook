# ChatGPT — MCP Config Reference

> 📖 Official docs: [platform.openai.com — Developer Mode](https://platform.openai.com/docs/guides/developer-mode)
> 📖 MCP server guide: [platform.openai.com — MCP](https://platform.openai.com/docs/mcp)

ChatGPT mendukung MCP via **Developer Mode** — fitur beta yang dirilis September 2025. Berbeda dari semua host lain di repo ini, ChatGPT **hanya support remote MCP server** via Streamable HTTP. Tidak ada stdio, tidak ada local process. Server harus bisa diakses dari internet dengan URL publik.

## Keterbatasan Penting

| Aspek | ChatGPT | Host lain (Claude, VS Code, dll) |
|-------|---------|----------------------------------|
| Transport | HTTP remote only | stdio + HTTP |
| Local server | ❌ Tidak support | ✅ Support |
| URL requirement | Butuh URL publik | Bisa localhost |
| Auth | OAuth 2.1 atau API key | Env var |
| Scope isolation | ❌ Global | ✅ (Claude Code) |

Ini implikasi besar untuk SOC use case: server seperti Wazuh atau TheHive yang berjalan di jaringan internal **tidak bisa langsung dihubungkan** ke ChatGPT tanpa exposed ke internet atau melalui tunnel.

## Setup Developer Mode

1. Buka **ChatGPT Settings** → **Beta Features**
2. Aktifkan **Developer Mode**
3. Di conversation, klik ikon `⚙️` atau buka settings server
4. Tambahkan MCP server URL

Atau via ChatGPT sidebar:

`Settings → Connected Apps → Add MCP Server → masukkan URL server`

## Config

ChatGPT tidak pakai file JSON seperti host lain. Semua konfigurasi dilakukan via UI — masukkan URL server dan credentials secara manual.

**Yang diperlukan:**
- URL server yang accessible dari internet (contoh: `https://your-mcp-server.example.com/mcp`)
- Auth token atau OAuth credentials (jika server memerlukan auth)

## Transport Types

### Streamable HTTP (satu-satunya yang didukung)

Server harus mengekspos endpoint HTTP yang compatible dengan MCP spec 2025-06-18.

Contoh URL yang valid:
```
https://your-server.example.com/mcp
https://api.your-domain.com/virustotal/mcp
```

URL `localhost` atau IP internal **tidak akan bisa diakses** oleh ChatGPT.

## Opsi untuk Server Internal

Kalau perlu connect ke tools SOC yang ada di internal network, ada beberapa pendekatan:

**Opsi 1 — Cloudflare Tunnel (gratis)**

```bash
# Expose local MCP server ke internet via Cloudflare Tunnel
cloudflared tunnel --url http://localhost:8080
```

Cloudflare akan generate URL publik yang forward ke local server. Tidak perlu buka port di firewall.

**Opsi 2 — Deploy server ke cloud**

Deploy MCP server ke VPS atau cloud instance yang punya IP publik. Server query ke internal tools via VPN atau private network.

**Opsi 3 — Pakai host lain**

Untuk kebutuhan SOC dengan tools internal, Claude Desktop atau Claude Code lebih cocok — keduanya support stdio dan localhost tanpa konfigurasi tambahan.

## Real Example: VirusTotal via ChatGPT

Untuk tools seperti VirusTotal yang memang punya hosted MCP endpoint:

```
Server URL: https://mcp.virustotal.com/mcp
Auth: Bearer <your-vt-api-key>
```

Masukkan langsung via ChatGPT Developer Mode settings UI.

## ⚠️ Security Considerations

**Server tidak diverifikasi OpenAI**

ChatGPT menampilkan peringatan ini saat connect ke custom MCP server: *"Custom connectors are not verified by OpenAI. Malicious developers may attempt to steal your data."* — ini artinya berlaku juga sebaliknya: server yang kamu tambahkan bisa menerima data dari conversation ChatGPT kamu.

**Jangan expose internal tools tanpa auth**

Kalau menggunakan tunnel untuk expose internal SIEM atau TheHive ke ChatGPT, pastikan server punya auth layer yang kuat. Endpoint tanpa auth yang bisa diakses publik adalah risiko serius.

**Data sensitivity**

Semua data yang dikirim ke MCP server via ChatGPT juga melewati infrastruktur OpenAI. Untuk investigasi yang bersifat confidential atau data internal sensitif, pertimbangkan pakai Claude Desktop atau Open WebUI dengan self-hosted LLM.

**Tool annotations wajib**

OpenAI mengharuskan setiap tool di MCP server yang connect ke ChatGPT untuk menyertakan annotations:
- `readOnlyHint: true` — untuk tool yang hanya baca data
- `destructiveHint: true` — untuk tool yang bisa delete/overwrite
- `openWorldHint` — untuk tool yang bisa akses resource di luar bounded target

Server tanpa annotations yang benar bisa ditolak atau tidak berfungsi di ChatGPT.

> Full security analysis → [../security-notes/attack-surface-per-client.md](../security-notes/attack-surface-per-client.md)
