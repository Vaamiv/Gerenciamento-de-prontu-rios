<img width="806" height="952" alt="image" src="https://github.com/user-attachments/assets/0bb673e5-24a7-4d74-b4b8-cd8135e84b37" />

# Sistema de Gerenciamento de Prontuários  
*(HTML + Tailwind + IndexedDB, 100% client-side)*

Este projeto é um **aplicativo estático** para cadastrar, consultar e gerenciar **prontuários em PDF** direto no navegador, com armazenamento local via **IndexedDB** (dados permanecem no dispositivo). Não há backend.

> **Privacidade**: os PDFs e metadados ficam **apenas no seu computador**/navegador. Nada é enviado para servidores.

---

## ✨ Funcionalidades

- **Cadastro de prontuários** (nome, data, tipo, categoria + PDF)
- **Consulta por paciente** com:
  - Busca por nome/tipo/categoria  
  - Filtros por **Tipo** e **Categoria**
  - Ordenação **Mais recentes / Mais antigos**
- **Download, edição e remoção** de registros
- **Exportar backup** (`.zip`) e **Importar backup** (`.zip`)
- **Migração automática** de bases antigas salvas em `localStorage`
- Validação de **PDF** (sem limite fixo de tamanho — sujeito ao limite do navegador)
- UI responsiva com Tailwind CSS

---

## 🗂 Estrutura

É um único arquivo:

```
index.html
```

Bibliotecas via CDN:
- Tailwind CSS
- Font Awesome (ícones)
- JSZip (exportação/importação de backup)

> Se desejar funcionamento **100% offline** (inclusive sem internet na primeira abertura), baixe os arquivos das CDNs e troque as referências por arquivos locais.

---

## 🚀 Como usar (local)

1. Baixe/salve o `index.html`.
2. Dê **duplo clique** para abrir no navegador (Chrome/Edge/Firefox).
3. Use as abas:
   - **Adicionar Prontuário**: preencha os campos e anexe o PDF.
   - **Consultar Pacientes**: pesquise, filtre, baixe, edite ou remova.

> A primeira abertura precisa de internet por causa das CDNs. Depois, os dados ficam salvos no navegador via IndexedDB.

---

## 💾 Armazenamento & Backup

### Onde os dados ficam?
- **IndexedDB** do navegador (DB: `medicalDB`, store: `records`).

### Exportar Backup
- Clique em **Exportar** → baixa um `.zip` com:
  ```
  metadata.json
  files/<id>__<nome_arquivo.pdf>
  ```

### Importar Backup
- Clique em **Importar** e selecione um `.zip` exportado por este sistema.  
- IDs duplicados são resolvidos gerando novos IDs quando necessário.

### Migração automática (localStorage → IndexedDB)
Na primeira carga, o app procura por chaves antigas:
- `medicalRecords`, `records` ou `prontuarios`
- Converte para IndexedDB (incluindo PDFs salvos como DataURL, se houver).

---

## 🧭 Fluxos principais

### Cadastrar
1. **Nome do paciente**  
2. **Data do atendimento**  
3. **Tipo**: `FAA`, `Internação`, `Oftalmologia`  
4. **Categoria**: `SUS`, `Particular HSP`, `IPE`, `PM Lagoa`, `Unimed`, `Outros`  
5. **PDF** (apenas valida a extensão/tipo)  
6. **Registrar Prontuário** → modal para adicionar outro do mesmo paciente

### Consultar
- **Busca livre** (nome/tipo/categoria)
- **Filtros**: Tipo e Categoria
- **Ordenação**: botão “**Mais recentes** / **Mais antigos**”
- **Ações por registro**: Download / Editar / Remover

---

## 🧪 Navegadores suportados

- Chrome (recomendado), Edge, Firefox, Safari (versões recentes)
- É necessário suporte a **IndexedDB** e **Blob**/File APIs.

---

## 🔐 Privacidade & LGPD

- Os arquivos e metadados ficam **somente no dispositivo** do usuário.  
- Tenha atenção ao uso em máquinas compartilhadas.
- Faça **backups** regulares e armazene-os com segurança.

---

## 🛜 Publicação

Como é só um `index.html`, você pode hospedar em:
- GitHub Pages / Netlify / Vercel / servidor estático qualquer.
- **Dica**: inclua os arquivos das CDNs localmente para funcionar offline “do zero”.

---

## 🧾 Licença

Uso livre para fins internos. Se desejar, você pode aplicar **MIT**.

---
