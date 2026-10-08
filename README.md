# Vistoria de Trafos

Site para os prospectores validarem em campo os trafos de referência que vão receber medição fiscal. Cada ficha tem fotos, checklist e parecer (Apto / Não apto / Revisar). Os relatórios saem em Excel, PDF ou CSV.

- **Site:** GitHub Pages (gratuito)
- **Banco de dados e fotos:** Supabase (gratuito para este volume)

```
index.html            o site completo
config.js             URL e chave do Supabase (você preenche)
supabase/schema.sql   cria tabelas, regras de segurança, bucket de fotos e carrega os 31 trafos
.nojekyll             necessário para o GitHub Pages
```

---

## Passo 1: criar o banco no Supabase (uns 10 minutos)

1. Acesse **supabase.com**, crie uma conta e clique em **New project**.
   - Nome: `vistoria-trafos`
   - Senha do banco: crie uma senha forte e guarde
   - Região: **South America (São Paulo)**
2. Com o projeto criado, abra **SQL Editor > New query**.
3. Abra o arquivo `supabase/schema.sql`, copie todo o conteúdo, cole no editor e clique em **Run**.
   - Ao final deve aparecer *Success*.
   - Em **Table Editor > trafos** devem aparecer os 31 trafos.
4. Vá em **Project Settings > API** (em alguns painéis aparece como **Data API / API Keys**) e copie dois valores:
   - **Project URL**, por exemplo `https://abcdefgh.supabase.co`
   - A chave **anon** (ou **publishable**)
   - **Nunca** use a chave `service_role` / `secret` no site.

## Passo 2: configurar o site (já feito para o projeto rxsvowkfinprmtkucghf)

Abra o `config.js` e cole os dois valores:

```js
window.VISTORIA_CONFIG = {
  SUPABASE_URL: "https://abcdefgh.supabase.co",
  SUPABASE_ANON_KEY: "eyJhbGciOi..."
};
```

## Passo 3: subir no GitHub e publicar

**Pelo navegador (sem instalar nada):**

1. No GitHub, clique em **New repository**.
   - Nome: `vistoria-trafos`
   - Marque **Public**. O GitHub Pages gratuito só funciona com repositório público.
   - Clique em **Create repository**.
2. Clique em **uploading an existing file** e arraste **todo o conteúdo** da pasta: `index.html`, `config.js`, `README.md`, `.nojekyll` e a pasta `supabase`. Depois clique em **Commit changes**.
   - O `.nojekyll` começa com ponto e pode estar oculto no seu computador. Se não aparecer, crie no GitHub um arquivo vazio com esse nome (**Add file > Create new file**).
3. Vá em **Settings > Pages**.
   - Em *Source*, escolha **Deploy from a branch**.
   - Branch **main**, pasta **/(root)**.
   - Clique em **Save**.
4. Em 1 a 2 minutos o endereço aparece no topo da página, por exemplo `https://SEU-USUARIO.github.io/vistoria-trafos/`. Esse é o link para mandar aos prospectores.

**Pelo terminal (se preferir git):**

```bash
cd vistoria-trafos
git init && git add . && git commit -m "Vistoria de Trafos"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/vistoria-trafos.git
git push -u origin main
```

Depois, ative o Pages como no item 3 acima.

---

## Segurança: o que está protegido e o que não está

O site funciona **sem login**, como foi pedido. Por isso, qualquer pessoa que tiver o link consegue ver as vistorias, preencher fichas e enviar fotos. A chave anon do `config.js` fica visível no site e no repositório. Isso é normal no Supabase: quem protege os dados são as regras do banco.

**O que as regras impedem pelo site:**

| Pelo site é possível | Pelo site **não** é possível |
|---|---|
| Ver a base de trafos | Alterar ou apagar a base de trafos |
| Ver, criar e editar vistorias | **Apagar** vistorias |
| Enviar fotos (só JPEG, até 10 MB) | Apagar ou substituir fotos |
| | Ver o histórico |

**Proteções extras:**

- **Histórico completo:** toda gravação fica salva na tabela `vistorias_historico`, com data e hora do servidor. Se alguém sobrescrever uma ficha, a versão anterior pode ser recuperada no painel do Supabase.
- **Aviso de conflito:** se outra pessoa salvou a mesma ficha depois que você a abriu, o site avisa antes de substituir.
- **Rascunho no celular:** se a internet cair ou o prospector sair da ficha sem salvar, o rascunho fica guardado no aparelho e volta quando ele abrir a ficha de novo.
- **Fora do Google:** o site tem `noindex`, então não aparece em buscas.

**Riscos que continuam:**

- Quem tiver o link pode alterar fichas de outros prospectores. As alterações ficam no histórico, mas não são bloqueadas.
- As fotos ficam em um bucket público. O endereço de cada foto é difícil de adivinhar, mas quem tiver o endereço consegue abrir a foto.
- O repositório é público, então o código e a chave anon ficam visíveis. Os dados dos trafos e das vistorias **não** ficam no GitHub. Eles ficam só no Supabase.

Se quiser travar mais (login por prospector, cada um editando só as próprias fichas, fotos privadas), dá para adicionar o login do Supabase depois sem perder nenhum dado.

## Rotina do administrador

- **Ver os dados:** Supabase > **Table Editor > vistorias**.
- **Recuperar uma versão antiga:** abra **Table Editor > vistorias_historico**, filtre pela placa e copie os valores da versão desejada.
- **Apagar uma vistoria errada:** pelo Table Editor, porque o site não deixa.
- **Adicionar trafos:** **Table Editor > trafos > Insert row**, ou importe um CSV com as colunas `placa, municipio, subestacao, link_maps`.
- **Backup:** no site, use **Exportar relatório > Excel** com fotos com frequência, por exemplo no fim de cada dia de campo. O plano gratuito do Supabase não guarda backups para você baixar.
- **Plano gratuito do Supabase:** um projeto sem uso por 7 dias é pausado. Para reativar, basta entrar no painel e clicar em **Restore**. Durante a campanha de campo, o uso diário mantém o projeto ativo.
