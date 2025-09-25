<img width="946" height="954" alt="image" src="https://github.com/user-attachments/assets/3f6cf077-06cf-4bd8-8119-85bbf9399430" />

# Sistema de Gerenciamento de Prontuários  
*(HTML + Tailwind + IndexedDB, 100% client-side, com criptografia)*

Aplicativo **estático** para cadastrar, consultar e gerenciar **prontuários em PDF** direto no navegador.  
Os dados ficam no **IndexedDB** do próprio dispositivo. **Não há backend.**

> **Privacidade**: metadados e PDFs ficam **apenas no seu computador**.  
> **Segurança em repouso**: PDFs são **criptografados** com AES-GCM; a chave vem de uma senha local via PBKDF2-SHA256.

---

## ✨ Funcionalidades

- **Cadastro de prontuários** (nome, data, tipo, categoria + PDF)
- **Consulta por paciente** com:
  - Busca por nome/tipo/categoria
  - Filtros por **Tipo** e **Categoria**
  - Ordenação **Mais recentes / Mais antigos**
- **Download, edição e remoção** de registros
- **Exportar** backup (`.zip`) e **Importar** backup (`.zip`)
  - Exporta **sempre criptografado**
  - Importa **legado** (sem criptografia) e recriptografa automaticamente
- **Tela de Acesso** (criar senha / entrar) e **Bloquear** sessão
- **Trocar senha** com **recriptografia** automática de todos os PDFs
- **Migração automática** de bases antigas salvas em `localStorage`
- **Sem limite fixo de tamanho** de PDF (sujeito aos limites do navegador)
- UI responsiva com Tailwind CSS

---

## 🔐 Modelo de segurança

- **Criptografia**: AES-GCM com IV de 12 bytes único por arquivo  
- **Derivação de chave**: PBKDF2-SHA256 com **150.000 iterações**  
- **Credenciais salvas localmente**:
  - `auth.salt` (base64)
  - `auth.verifier` = SHA-256 da chave derivada (não é a senha)
- **Sessão**: botão **Bloquear** e **auto-bloqueio** após 10 min sem interação  
- **Backups**: `.zip` com `metadata.json` + `files/*.bin` (ciphertext)

> **Fora de escopo**: extensões maliciosas, máquina comprometida ou alguém usando DevTools **enquanto a sessão estiver desbloqueada**. O objetivo é proteger **em repouso** e exigir senha para operações sensíveis.

---

## 🗂 Estrutura

Único arquivo:

```
index.html
```

CDNs:
- Tailwind CSS
- Font Awesome (ícones)
- JSZip (backup)

> Para funcionar 100% offline “desde a 1ª abertura”, baixe os arquivos das CDNs e troque os `<script>/<link>` por referências locais.

---

## 🚀 Como usar (local)

1. Abra o `index.html` no navegador (Chrome/Edge/Firefox/Safari).  
2. Na primeira execução, **crie uma senha**.  
3. Use as abas:
   - **Adicionar Prontuário**: preencha os campos e anexe o PDF.
   - **Consultar Pacientes**: pesquise, filtre, baixe, edite ou remova.
4. Botões do topo:
   - **Exportar**: gera `.zip` **criptografado**.
   - **Importar**: restaura backups; também aceita **legado** (sem crypto).
   - **Bloquear**: encerra a sessão atual.

> A primeira abertura pode precisar de internet por causa das CDNs.

---

## 🔑 Trocar senha (com recriptografia)

1. Clique em **Mudar senha**.  
2. Informe **senha atual** e **nova senha** (mín. 6 chars).  
3. O app valida a senha e **recriptografa todos os PDFs** com a nova chave (pode levar alguns segundos se houver muitos arquivos).  

> **Esqueceu a senha?** Limpe o navegador e **reimporte um backup**. Você precisará da senha válida na época do backup.

---

## 💾 Armazenamento & Backup

### Onde os dados ficam?
- **IndexedDB** (DB: `medicalDB`, store: `records`)

Campos por registro (principais):
- `id`, `patientName`, `appointmentDate`, `appointmentType`, `category`
- `fileName`, `fileMime`
- `fileIv` (base64) → **presença indica que o arquivo está criptografado**
- `fileBlob` (Blob) → **ciphertext**
- `createdAt`

### Exportar
Gera `.zip` com:
```
metadata.json  // inclui encrypted: true, itens e metadados
files/<id>__<nome_arquivo>.bin  // conteúdo criptografado (AES-GCM)
```

### Importar
- **Novo formato** (criptografado): restaura direto.  
- **Legado** (PDF limpo): ao importar, **criptografa** com a sua chave atual.  
- IDs duplicados geram **novos IDs** automaticamente.

### Migração automática (localStorage → IndexedDB)
Na primeira carga, migra dados das chaves antigas:
- `medicalRecords`, `records` ou `prontuarios`
- Converte para IndexedDB (incluindo PDFs salvos como DataURL, se houver).

---

## 🧭 Fluxos principais

### Cadastrar
1. **Nome do paciente**
2. **Data do atendimento**
3. **Tipo**: `FAA`, `Internação`, `Oftalmologia`
4. **Categoria**: `SUS`, `Particular HSP`, `IPE`, `PM Lagoa`, `Unimed`, `Outros`
5. **PDF** (somente valida extensão/tipo)
6. **Registrar Prontuário** (modal oferece adicionar outro do mesmo paciente)

### Consultar
- **Busca livre** (nome/tipo/categoria)
- **Filtros**: Tipo e Categoria
- **Ordenação**: “**Mais recentes** / **Mais antigos**”
- **Ações**: Download / Editar / Remover

---

## 🧪 Navegadores suportados

- Chrome (recomendado), Edge, Firefox, Safari (recentes)
- Requer **IndexedDB**, **Blob/File APIs** e **Web Crypto API** (PBKDF2, AES-GCM, SHA-256).

---

## 🔐 Privacidade & LGPD

- Dados ficam **somente no dispositivo** do usuário.
- Cuidado em **máquinas compartilhadas** (use **Bloquear**).
- Faça **backups** regulares e armazene-os em local seguro.

---

## 🧾 Licença

Uso livre para fins internos.
