# Pronto Atendimento — Ouvidoria-Geral do Município de Londrina

Sistema de registro e análise dos atendimentos de pronto atendimento da OGM.
Substitui o Google Forms mensal por uma base única (todos os meses e anos),
com filtro em cascata órgão → assunto, consulta/edição/exclusão, painel analítico
e administração das tabelas pela própria tela.

**Arquitetura:** Google Sheets (base) → Apps Script (API) → GitHub → Vercel (front-end estático).

---

## 1. Planilha

1. Suba `BASE_PRONTO_ATENDIMENTO_OGM.xlsx` no Google Drive e abra com **Planilhas Google**
   (Arquivo → Salvar como Planilhas Google).
2. Apague a linha de exemplo `PA-2026-000001` da aba `ATENDIMENTOS`.
3. Na aba `PARAMETROS`, troque o valor de `CHAVE_ACESSO` por uma senha própria.
4. Copie o **ID da planilha** — é o trecho entre `/d/` e `/edit` na URL.

Abas: `ATENDIMENTOS` (base) · `TAXONOMIA` (órgão → assunto) · `PARAMETROS`
(servidores, formas, desfechos, chave) · `LOG` (auditoria) · `LEIA-ME`.

## 2. Apps Script

1. Na planilha: **Extensões → Apps Script**.
2. Apague o conteúdo padrão e cole `Code.gs`.
3. Substitua `COLE_AQUI_O_ID_DA_PLANILHA` pelo ID copiado.
4. **Implantar → Nova implantação → App da Web**
   - Executar como: **Eu**
   - Quem pode acessar: **Qualquer pessoa**
5. Copie a URL terminada em `/exec`.

> Toda alteração no `Code.gs` exige **Implantar → Gerenciar implantações → editar → Nova versão**.

## 3. Front-end

1. Em `config.js`, cole a URL `/exec` em `window.API_URL`.
2. Suba esta pasta para um repositório no GitHub (ex.: `web-ouvidoria`).
3. No Vercel: **Add New → Project → Import** o repositório.
   Framework Preset: **Other**. Root Directory: a pasta que contém o `index.html`.
   Sem build command, sem output directory.
4. Deploy. Sugestão de domínio: `web-ouvidoria.vercel.app`.

## Segurança

- Painel e consulta são **públicos** (leitura).
- Gravar, editar, excluir e administrar exigem a **chave de acesso** da aba `PARAMETROS`,
  digitada no topo da tela e guardada só na sessão do navegador.
- Toda inclusão, edição e exclusão é gravada na aba `LOG` com data, hora, servidor e conteúdo.

## Como o cadastro funciona

- **Data**: escolhida no calendário, gravada como texto `DD-MM-YYYY`. `MES` e `ANO` são derivados pelo sistema.
- **Assunto**: único por registro, restrito aos assuntos do órgão escolhido. Cada linha equivale a um atendimento.
- **Desfecho**: obrigatório, alimenta o painel.

## Manutenção das listas

Aba **Administração** do app (ou direto na planilha):
- incluir assunto novo em qualquer órgão;
- desativar assunto sem apagar o histórico (`ATIVO = NAO`);
- incluir ou desativar servidores, formas de recebimento e desfechos.

Órgão novo: basta incluir um assunto informando o nome do órgão novo no campo Órgão.

## Migração do histórico

Os formulários mensais anteriores podem ser consolidados na aba `ATENDIMENTOS` depois.
As colunas precisam terminar no mesmo layout: `ID, DATA, MES, ANO, SERVIDOR, FORMA_RECEBIMENTO,
ORGAO, ASSUNTO, DESFECHO, OBSERVACAO, TIMESTAMP, USUARIO`.
