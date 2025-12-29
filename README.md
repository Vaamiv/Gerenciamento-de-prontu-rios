<img width="838" height="953" alt="image" src="https://github.com/user-attachments/assets/92687616-a7fe-4cb4-af59-768f134a3999" />


# Sistema de Gerenciamento de Prontuários  
*(HTML + Tailwind + IndexedDB + Criptografia AES-GCM — 100% client-side)*

Aplicativo **estático** para cadastrar, consultar e gerenciar **prontuários em PDF** direto no navegador.  
Os dados ficam no dispositivo do usuário (**IndexedDB**) e os arquivos são criptografados localmente.

> **Privacidade**: PDFs e metadados **não saem do seu computador**. Nada é enviado para servidores.

---

## ✨ Funcionalidades

- **Cadastro de prontuários** (nome, data, tipo, categoria + PDF)
- **Consulta** com:
  - Busca por nome/tipo/categoria
  - Filtros por **Tipo** e **Categoria**
  - Ordenação cíclica: **Mais recentes → Mais antigos → A–Z → Z–A**
- **Ações por registro**:
  - **Visualizar** PDF em nova aba (descriptografado na memória)
  - **Download** do PDF
  - **Editar** metadados e substituir arquivo
  - **Remover** (vai para **Lixeira**) com opção **Desfazer**
- **Backup**:
  - **Exportar** `.zip` (com `metadata.json` e arquivos criptografados)
  - **Importar** `.zip` (aceita backups legados sem criptografia e os recriptografa)
  - **Pasta de backup** (opcional, via File System Access API — Chromium)
- **Segurança**:
  - Tela de **bloqueio/autenticação**
  - **Auto-bloqueio** após 10 min de inatividade
  - **Alterar senha** (recriptografa todos os PDFs com a nova senha)

---

## 🗂 Estrutura do projeto

Único arquivo:

```
index.html
```

CDNs utilizadas:
- Tailwind CSS
- Font Awesome (ícones)
- JSZip (backup)

> Para funcionar **100% offline desde a primeira execução**, baixe as dependências das CDNs e referencie os arquivos locais.

---

## 🚀 Como usar (local)

1. Baixe/salve o `index.html`.
2. Abra no navegador (Chrome/Edge/Firefox/Safari).
3. Na **primeira execução**, crie a **senha** (somente neste navegador).
4. Use as abas:
   - **Adicionar Prontuário**: preencha campos e anexe o PDF.
   - **Consultar Pacientes**: pesquise, filtre, visualize, baixe, edite ou remova.
5. **Bloquear** quando quiser (botão **Bloquear**) ou aguarde auto-bloqueio (10 min).

---

## 🔐 Criptografia & Autenticação

- Cada PDF é criptografado com **AES-GCM** e **IV aleatório**.
- A chave é derivada da senha via **PBKDF2-SHA256** (`150.000` iterações).
- No `localStorage` ficam apenas:
  - `auth.salt` — *salt* em Base64
  - `auth.verifier` — `SHA-256` da chave derivada (para verificar a senha)
- Os blobs criptografados são armazenados na **IndexedDB**.

### Alterar senha
1. Clique em **Alterar senha**.
2. Informe **senha atual**, **nova senha** e **confirmação**.
3. O app descriptografa cada arquivo com a chave antiga e **recriptografa** com a nova.
4. Atualiza `auth.salt` e `auth.verifier` e passa a usar a nova chave.

> **Importante:** se esquecer a senha, **não há recuperação**. Limpe o navegador e **importe** um backup válido.

---

## 💾 Armazenamento & Backup

### IndexedDB
- Banco: `medicalDB`
- Stores:
  - `records` (prontuários)
  - `settings` (ex.: handle da **pasta de backup** quando suportado)
- Exemplo de registro (`records`):
```json
{
  "id": "uuid",
  "patientName": "Fulano",
  "appointmentDate": "2025-01-31",
  "appointmentType": "FAA",
  "category": "SUS",
  "fileName": "arquivo.pdf",
  "fileMime": "application/pdf",
  "fileIv": "Base64(IV)",             // ausente em backups legados sem criptografia
  "fileBlob": "[Blob criptografado]",
  "createdAt": "2025-01-31T12:34:56.000Z",
  "deletedAt": null                   // preenchido quando vai para a Lixeira
}
```

### Exportar backup
Gera um `.zip` com:
```
metadata.json
files/<id>__<nome_sanitizado>.bin   # blobs criptografados
```
`metadata.json` (exemplo):
```json
{
  "encrypted": true,
  "exportedAt": "2025-01-31T12:34:56.000Z",
  "count": 2,
  "items": [
    {
      "id": "uuid",
      "patientName": "Fulano",
      "appointmentDate": "2025-01-31",
      "appointmentType": "FAA",
      "category": "SUS",
      "fileName": "arquivo.pdf",
      "fileMime": "application/pdf",
      "fileIv": "Base64(IV)",
      "createdAt": "2025-01-31T12:00:00.000Z",
      "deletedAt": null
    }
  ]
}
```

### Importar backup
- Seleciona `.zip` exportado pelo sistema.
- Se encontrar arquivos **legados sem criptografia**, eles são **criptografados** na importação (exige senha desbloqueada).
- Conflito de IDs gera **novo ID** automaticamente.

### Pasta de backup (opcional)
- Em navegadores Chromium (Chrome/Edge) é possível escolher uma **pasta** para gravar o `.zip` automaticamente.
- A permissão do diretório é requisitada pelo navegador; pode ser revogada a qualquer momento.

---

## 🧭 Fluxos principais

### Cadastrar
1. **Nome do paciente**
2. **Data do atendimento**
3. **Tipo**: `FAA`, `Internação`, `Oftalmologia`
4. **Categoria**: `SUS`, `Particular HSP`, `IPE`, `PM Lagoa`, `Unimed`, `Outros`
5. **PDF**
6. **Registrar Prontuário** → modal para adicionar outro do mesmo paciente

### Consultar
- **Busca** livre (nome/tipo/categoria)
- **Filtros**: Tipo e Categoria
- **Ordenação**: **Mais recentes → Mais antigos → A–Z → Z–A**
- **Ações**: **Visualizar** / **Download** / **Editar** / **Remover** (Lixeira + **Desfazer**)

---

## 🧪 Navegadores suportados

- **Chrome/Edge** (recomendados), **Firefox**, **Safari** (versões recentes).
- Requisitos: **IndexedDB**, **File/Blob APIs**, **Web Crypto (AES-GCM, PBKDF2)**.
- **Pasta de backup** requer **File System Access API** (Chromium).

---

## ⚠️ Observações de segurança

- O app é **local** e não substitui exigências regulatórias (ex.: LGPD/HIPAA/segurança corporativa).
- Proteja o **dispositivo** (usuário do SO, disco criptografado, tela com senha).
- Em **navegação anônima** os dados podem ser perdidos ao fechar a janela.
- **Esqueceu a senha?** Limpe o navegador e **importe** um backup. A equipe não consegue recuperar.

---

## 🧾 Licença

Uso livre para fins internos. Se preferir, aplique **MIT**.
