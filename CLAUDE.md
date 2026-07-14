# CLAUDE.md — Manual de Língua Portuguesa

Manual completo de Língua Portuguesa (português brasileiro) da série **Manuais de Ciências**.
Livro Quarto (HTML + PDF) publicado em GitHub Pages via GitHub Actions.

## Modo de execução: AUTÔNOMO

Você está pré-aprovado para, sem pausar para pedir aprovação:
- criar e editar arquivos `.qmd`, `.yml`, `.css`, `.bib`, `.md`;
- criar figuras TikZ e SVG;
- rodar comandos de validação (`quarto render`, `quarto preview`);
- fazer `git commit` ao final de cada capítulo.

**Pare apenas em erros verdadeiramente bloqueantes** (render quebrado que você não consegue corrigir, conflito de git, arquivo essencial ausente).

- Formato de commit: `cap NN: <título do capítulo>` (ex.: `cap 07: Fonema e letra`).
- Commits de infraestrutura: `infra: <descrição>`.
- **Windows**: usar apenas flags simples `git commit -m "..."`. **NUNCA** usar here-strings do PowerShell (`@'...'@`) dentro do Bash — o `@` vaza para a mensagem de commit.

## Workflow de sessão

- **Um capítulo por sessão.** Ao terminar: commit, atualizar `ROADMAP.md`, encerrar.
- `ROADMAP.md` é a fila executável autoritativa: contém caminho de arquivo e status de cada capítulo. Sempre consultá-lo para saber o próximo capítulo; sempre atualizá-lo ao concluir.
- Estratégia de fatia vertical: completar um volume inteiro antes de abrir o próximo; completar a Fase 1 antes da Fase 2.
- Modelo: `opus` para escrever capítulos; `sonnet` para tarefas mecânicas (`_quarto.yml`, status no ROADMAP).

## Bootstrap do toolchain (executar no início de TODA sessão antes de qualquer render)

1. **PATH do TinyTeX** (`quarto install tinytex` NÃO adiciona o bin ao PATH da sessão):
   - Windows: prepender `$HOME/AppData/Roaming/TinyTeX/bin/windows` ao PATH.
   - Linux/macOS: prepender `~/.TinyTeX/bin/<plataforma>` ao PATH.
   - Sintoma da falta disso: figura TikZ falha, `tikz.lua` com erro `imgdata nil` por volta da linha 587. **É problema de PATH, não de pacote faltando.**
2. Se for a primeira sessão na máquina: `quarto install tinytex` e depois
   `tlmgr install standalone pgf pgfplots dvisvgm xcolor amsmath amsfonts`.
3. Validar com `quarto render --to html` de um capítulo pequeno antes de escrever conteúdo novo, se houver dúvida sobre o ambiente.

## Regras invioláveis do toolchain

1. **Extensão TikZ — NUNCA atualizar.** O projeto usa `danmackinlay/tikz` com **patches locais** (origem: `figuras-tikz-kit.zip` / `FIGURAS.md`). **NUNCA rodar `quarto add` ou `quarto update`** — isso baixa o upstream sem os patches e quebra o render. Os arquivos vivem em `_extensions/danmackinlay/tikz/`.
2. Em `_quarto.yml`: o filtro `danmackinlay/tikz` deve vir **antes** de `quarto`; configurar `tikz: svg-engine: dvisvgm`.
3. **`lang: pt`** deve estar na **raiz** do `_quarto.yml`, nunca aninhado em `book:`.
4. **`styles.css`** listado em `theme:` deve começar com o marcador `/*-- scss:rules --*/` (Quarto ≥ 1.9 o trata como camada SCSS; sem o marcador, o render inteiro quebra com erro de "layer boundary").
5. **CI (`.github/workflows/publish.yml`)**: incluir o passo `run: quarto install chrome-headless-shell` **antes** do render/publish (o grafo mermaid do `index.qmd` trava o render de PDF em runners Ubuntu sem Chromium). Usar `chrome-headless-shell` especificamente, **não** `chromium`. No CI, após setup de Quarto+TinyTeX, localizar `tlmgr` via `find` se não estiver no PATH e rodar o mesmo `tlmgr install` do bootstrap.
6. **Branch `gh-pages` deve pré-existir** antes do primeiro workflow (`quarto-actions/publish@v2` aborta sem ela). Correção única:
   ```
   git push origin $(git commit-tree $(git hash-object -t tree /dev/null) -m 'init gh-pages'):refs/heads/gh-pages
   ```

## Pacotes LaTeX — exclusões deste manual

- **NUNCA usar**: `siunitx`, `physics`, `bussproofs`, `tipa` (quebram HTML/MathJax).
- Transcrição fonética: caracteres Unicode IPA diretamente no texto (ex.: /ˈkazɐ/, [ˈt͡ʃiɐ]). Nunca pacote LaTeX de fonética.
- Matemática só aparece incidentalmente (versificação, estatística de variação): MathJax/LaTeX padrão.

## Figuras

- **TikZ** para todo diagrama esquemático/didático: árvores sintáticas, esquemas morfológicos, trapézio vocálico, diagramas de período composto, linhas do tempo da história da língua, mapas esquemáticos de variação.
- Sintaxe obrigatória: div `::: {#fig-nome}` contendo bloco `{.tikz}` com opções `%%| filename:` e `%%| alt:`; legenda antes do fechamento do div; referenciar no texto via `@fig-nome`.
- Estilos predefinidos do kit: `curva`, `destaque`, `auxiliar`, `eixo`, `ponto`, `vetor`; cores: `manualblue`, `manualred`, `manualgreen`, `manualyellow`, `manualgray`.
- Conteúdo que exigiria fotografia (mapas reais, reproduções de manuscritos): usar placeholder documentado, nunca inventar imagem.
- Grafo de pré-requisitos dos volumes no `index.qmd`: **mermaid**.

## Anatomia obrigatória de cada capítulo (.qmd)

Seguir fielmente o **capítulo gabarito** (`capitulos/cap-01-linguagem-lingua-fala.qmd`). Estrutura:

1. **Abertura**: título (`# ...`), epígrafe curta opcional com fonte, 1–2 parágrafos de contextualização motivadora.
2. **🎯 Objetivos de aprendizagem**: 3–6 itens iniciados por verbo.
3. **Desenvolvimento**: seções `##` e subseções `###`; exemplos autênticos do português brasileiro em todos os conceitos; quando pertinente, contrastar norma-padrão × uso real do PB, sem prescritivismo ingênuo nem relativismo vazio.
4. **Figuras TikZ** onde um esquema ajudar (mínimo 1 por capítulo quando o tema for visualizável).
5. **📋 Quadro-resumo**: tabela ou lista sintética.
6. **⚠️ Pegadinhas e erros comuns**: foco em vestibular/concurso, com explicação do porquê.
7. **✍️ Exercícios** (seção obrigatória): 8–12 questões, misturando fixação e estilo de banca (ENEM, FUVEST, FCC, Cebraspe — questões autorais "no estilo de", nunca copiadas). Cada questão com **solução comentada em bloco colapsável**:
   ```
   ::: {.callout-tip collapse="true"}
   ## Solução
   ...
   :::
   ```
8. **📚 Referências**: citações no corpo via `@chave`; entradas em `referencias.bib`; estilo ABNT (CSL).

## Estilo de escrita

- Português brasileiro culto, tom professoral acolhedor, segunda pessoa indireta ("observe que...", "note como...").
- Rigor conceitual com leveza: definir tecnicamente, ilustrar imediatamente com exemplo real.
- Terminologia da NGB (Nomenclatura Gramatical Brasileira) como padrão, mencionando nomenclaturas alternativas quando relevantes (ex.: Castilho, Perini).
- Referencial teórico: Bechara, Cunha & Cintra (norma); Castilho, Perini, Ilari (descrição); Bagno, Faraco (variação); Koch, Marcuschi (texto); Câmara Jr., Teyssier (história).
- Extensão-alvo por capítulo: 2.500–4.500 palavras + exercícios.
- Nunca deixar seção obrigatória vazia; nunca escrever "em construção" em capítulo marcado como concluído.

## Checklist de conclusão de capítulo

1. Todas as 8 seções da anatomia presentes.
2. `quarto render capitulos/cap-NN-*.qmd --to html` sem erros e sem warnings novos.
3. Figuras TikZ renderizando (verificar SVG gerado).
4. Todas as `@chave` citadas existem em `referencias.bib`.
5. Capítulo listado em `_quarto.yml` (parte/volume correto).
6. `ROADMAP.md` atualizado (status ✅ + data).
7. Commit `cap NN: <título>`.
