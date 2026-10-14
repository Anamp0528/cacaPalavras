# Projeto técnico — jogo de agrupamento de palavras

**Produto:** jogo casual de agrupamento de palavras  
**Direção de interface:** protótipo 03 — Órbita  
**Status:** proposta técnica para orientar a implementação; não substitui decisões pendentes do PRD  
**Referência funcional:** [PRD.md](./PRD.md)  
**Data:** 14 de outubro de 2026

## 1. Objetivo e escopo

Este documento descreve uma abordagem para transformar o protótipo visual Órbita em um jogo web funcional, responsivo e acessível. O MVP deve permitir partidas ilimitadas sem conta, agrupar 16 palavras em quatro grupos de quatro, oferecer os modos relaxado e com limite de erros, apresentar uma pista opcional após progresso e compartilhar um resumo sem spoilers.

O documento separa:

- **Estado atual:** um protótipo exploratório em `index.html`, feito com HTML, CSS e JavaScript, sem framework ou pipeline de build identificado.
- **Proposta para o MVP:** continuar com tecnologias web nativas, organizar o código em módulos e usar geração combinatória a partir de conteúdo lexical estruturado. Automatizar a montagem dos puzzles não significa confiar em geração livre de texto sem controle.
- **Decisões pendentes:** regras que o PRD ainda não fixou, como limite de erros, política exata de pistas e formato final de compartilhamento. Este plano propõe pontos de integração para essas decisões, sem tratá-las como aprovadas.

## 2. Princípios técnicos

1. **Regras do jogo independentes da interface.** A lógica deve poder ser testada sem DOM, CSS ou navegador.
2. **Conteúdo validado antes de ser jogado.** A geração deve compor categorias e palavras previamente estruturadas, rejeitando combinações inválidas.
3. **Progressão local e sem conta.** O MVP não deve depender de servidor para iniciar, concluir ou retomar uma partida.
4. **Acessibilidade como requisito de implementação.** Cor, movimento ou interação por mouse não podem ser os únicos meios de comunicar ou operar o jogo.
5. **Direção Órbita sem sacrificar legibilidade.** Tema escuro e acento violeta são identidade visual; contraste, foco, tamanho de texto e toque continuam prioritários.
6. **Regras não definidas permanecem configuráveis.** Não codificar valores provisórios de erros ou pistas como regras definitivas.
7. **Falhas explícitas.** Se não houver um puzzle válido ou não for possível salvar/compartilhar, informar a situação sem simular sucesso.

## 3. Arquitetura recomendada

### 3.1 Visão geral

Para o MVP, recomenda-se uma aplicação estática no navegador com JavaScript em módulos ES, HTML semântico e CSS próprio. O jogo não precisa de uma API nem de autenticação para cumprir o escopo atual. Um build leve pode ser introduzido para empacotamento, testes e distribuição dos módulos, sem impor framework de interface.

```text
Navegador
├── Interface Órbita (HTML semântico + CSS responsivo)
├── Aplicação (coordena eventos, estado e renderização)
├── Domínio (seleção, validação de jogada, progresso e conclusão)
├── Gerador de puzzles (composição e verificações de conteúdo)
├── Persistência local (LocalStorage, com versão de esquema)
└── Compartilhamento (Web Share API, clipboard e alternativa manual)
```

**Sem backend no MVP:** puzzle, respostas, progresso e preferências permanecem no dispositivo. Uma API só deve ser introduzida se surgir necessidade validada de catálogo remoto, atualização de conteúdo, sincronização, telemetria consentida ou administração editorial.

### 3.2 Organização de arquivos sugerida

```text
/
├── index.html
├── src/
│   ├── app.js
│   ├── domain/
│   │   ├── game.js
│   │   ├── puzzle-generator.js
│   │   ├── puzzle-validator.js
│   │   └── game-state.js
│   ├── content/
│   │   ├── categories.json
│   │   └── vocabulary.json
│   ├── storage/
│   │   └── local-game-storage.js
│   ├── sharing/
│   │   └── share-result.js
│   ├── ui/
│   │   ├── render-game.js
│   │   ├── render-board.js
│   │   └── announce-status.js
│   └── styles/
│       ├── tokens.css
│       ├── layout.css
│       └── components.css
├── tests/
│   ├── domain/
│   ├── accessibility/
│   └── e2e/
└── public/
    └── icons/
```

Essa é uma estrutura-alvo. Não é necessário criar todos os arquivos antes de implementar; extrair cada módulo quando sua responsabilidade existir. Evitar concentrar regras de domínio, conteúdo e persistência no mesmo script inline do HTML, como ocorre no protótipo exploratório.

## 4. Tecnologias e ferramentas

| Área | Proposta para o MVP | Observações |
|---|---|---|
| Marcação | HTML semântico | Botões nativos para palavras e ações; regiões com títulos descritivos. |
| Estilos | CSS moderno, custom properties e media queries | Tokens próprios para a identidade Órbita; sem biblioteca visual necessária. |
| Lógica | JavaScript com módulos ES | Não requer framework para esta interface de uma tela. Tipos JSDoc podem documentar estruturas críticas. |
| Conteúdo | JSON versionado junto da aplicação | Manter procedência e licença de cada fonte lexical. |
| Persistência | `localStorage` com versão de esquema | Adequado ao volume pequeno do MVP; envolver leitura/escrita em tratamento de erro explícito. |
| Build/servidor local | Vite (proposta) | Simplifica servidor de desenvolvimento, importação de JSON e build estático; adotar somente após confirmar a configuração do repositório. |
| Testes unitários | Vitest (proposta) | Aplicar à lógica de seleção, geração, validação, persistência e compartilhamento. |
| Testes de navegador | Playwright (proposta) | Validar fluxo de ponta a ponta, larguras responsivas e interações. |
| Automação de acessibilidade | axe-core em testes de navegador (proposta) | Complementa, não substitui, avaliação manual com teclado e leitores de tela. |

O diretório atual não apresenta manifesto de dependências. A instalação de ferramentas deve ocorrer somente quando a implementação de código for iniciada e a equipe aprovar essas propostas.

## 5. Modelo de conteúdo e geração de puzzles

### 5.1 Modelo

O gerador deve operar sobre unidades de conteúdo aprovadas para uso, não sobre palavras aleatórias. A hipótese recomendada é manter um catálogo de categorias e conjuntos lexicais estruturados; o algoritmo seleciona quatro grupos compatíveis para formar cada puzzle. Isso permite geração automática sem revisão manual de cada partida, embora a própria base de categorias e palavras ainda exija procedência, validação e manutenção.

Exemplo conceitual de dados:

```json
{
  "id": "objetos-que-giram",
  "label": "Objetos que giram",
  "difficulty": 1,
  "locale": "pt-BR",
  "words": ["catavento", "pião", "roda", "ventilador"],
  "explanation": "Objetos que podem girar em torno de um eixo ou ponto.",
  "tags": ["objetos", "movimento"],
  "contentVersion": 1
}
```

Campos como `difficulty`, `tags` e `explanation` são uma proposta de modelagem. A forma de calcular dificuldade e verificar clareza precisa ser definida antes de se tornar critério automático de publicação.

### 5.2 Algoritmo de composição proposto

1. Carregar categorias elegíveis para o idioma e a versão de conteúdo.
2. Filtrar entradas inválidas: contagem diferente de quatro palavras, duplicatas após normalização, campos obrigatórios ausentes, vocabulário bloqueado ou licença/procedência desconhecida.
3. Selecionar quatro categorias usando semente aleatória local, equilibrando faixas de dificuldade quando os metadados forem confiáveis.
4. Rejeitar uma combinação se houver palavras compartilhadas, colisões normalizadas ou categorias marcadas como semanticamente incompatíveis.
5. Aplicar verificações automáticas para grupos muito próximos, por exemplo, palavras coincidentes, tags sobrepostas ou relações duplicadas segundo metadados disponíveis.
6. Repetir a amostragem até um número máximo de tentativas definido por configuração.
7. Validar o puzzle final: exatamente 16 palavras únicas, quatro grupos com quatro palavras, uma relação esperada por conjunto e ausência de conflitos conhecidos.
8. Se o limite de tentativas for excedido, emitir erro controlado e apresentar uma alternativa de catálogo válida ou mensagem de indisponibilidade. Nunca apresentar um puzzle parcialmente validado.
9. Embaralhar a ordem visual das palavras separadamente da ordem interna das respostas.

As verificações automáticas são filtros, não prova de unicidade semântica. A ambiguidade residual é um risco conhecido no PRD. Cada puzzle deve manter um `puzzleId`, versão de conteúdo e semente não sensível para permitir diagnosticar problemas reproduzidos localmente. A pessoa não deve ver categorias ou palavras de grupos não resolvidos antes do encerramento definido pelo produto.

### 5.3 Vocabulário e localização

- Definir normalização para acentos, caixa, espaços, hífens, flexões e variantes aceitas.
- Preservar a forma de exibição original; usar forma normalizada apenas para comparar identidades e duplicatas.
- Registrar origem, licença, versão e restrições de uso de cada lista lexical.
- Não assumir que uma licença de consulta permite redistribuir uma cópia do dicionário no bundle.
- Definir política de palavras regionais, nomes próprios, marcas, termos ofensivos e conteúdo sensível.
- Incluir validações automáticas de termos proibidos, reconhecendo que filtros lexicais podem produzir falsos positivos e falsos negativos.
- Não enviar palavras do jogador a serviços de terceiros; no fluxo do MVP, a pessoa apenas seleciona conteúdo fornecido pelo jogo.

## 6. Modelo de estado e regras de domínio

### 6.1 Estado de partida

O domínio deve manter um estado explícito, por exemplo:

```text
GameState
├── schemaVersion
├── puzzleId
├── contentVersion
├── mode: "relaxed" | "limited"
├── status: "playing" | "won" | "lost"
├── words: lista das 16 palavras
├── groups: quatro grupos com categoria e palavras
├── solvedGroupIds: conjunto de grupos resolvidos
├── selectedWordIds: conjunto de no máximo quatro palavras
├── mistakes: inteiro não negativo
├── hintState
└── startedAt / updatedAt (opcionais, se aprovados)
```

O tipo `lost`, o limite de erros e o comportamento ao atingi-lo dependem da decisão de produto. Até isso ser decidido, não implementar transição de derrota definitiva. O estado `hintState` deve permitir aplicar no futuro o gatilho e o impacto definidos sem refazer o motor do jogo.

### 6.2 Transições

```text
playing
  ├── selecionar/desselecionar palavra → playing
  ├── confirmar seleção inválida (< 4) → sem transição; ação desabilitada
  ├── confirmar grupo correto → marca grupo resolvido; revela categoria
  ├── confirmar grupo incorreto → incrementa erros; mantém puzzle
  ├── solicitar pista quando habilitada → atualiza estado de pista
  └── todos os grupos resolvidos → won

won
  ├── pré-visualizar compartilhamento → diálogo de partilha
  └── iniciar outra partida → novo estado playing
```

`lost` só deverá ser adicionado após decisão explícita do PRD sobre limite e revelação das respostas restantes.

### 6.3 Regras de seleção

- A seleção deve conter no máximo quatro identificadores únicos de palavras ainda disponíveis.
- Selecionar uma palavra já selecionada remove-a da seleção.
- Não permitir selecionar palavras de grupos já resolvidos.
- Ao chegar a quatro seleções, desabilitar visual e funcionalmente as palavras restantes até a pessoa remover uma seleção ou confirmar.
- Confirmar só é permitido quando há exatamente quatro palavras selecionadas.
- Uma tentativa incorreta mantém as palavras disponíveis e limpa a seleção após feedback acessível.
- Um acerto retira as palavras da grade, revela categoria e atualiza progresso.
- As mesmas regras devem ser aplicadas pelo motor de domínio, não apenas por controles desabilitados na interface.

## 7. Interface Órbita e acessibilidade

### 7.1 Direção visual

- **Tema:** escuro, sereno e contemporâneo, com superfície de jogo em tom ameixa/azul profundo.
- **Paleta inicial do protótipo:** fundo `#191827`, superfície `#222136`, cartão `#29283E`, texto claro `#F5F3FF`, acento violeta `#A78BFA`.
- **Marca e estados:** usar o acento em seleção e ações; acerto e erro devem ter também ícone e texto.
- **Desktop:** painel de contexto/erros à esquerda, tabuleiro e instruções à direita.
- **Celular:** tabuleiro e ações primeiro; informações auxiliares empilhadas abaixo, sem rolagem horizontal.
- **Tipografia:** fontes do sistema, sem chamadas externas obrigatórias.
- **Movimento:** transições discretas e removidas/reduzidas por `prefers-reduced-motion`.

As cores indicadas são ponto de partida visual, não declaração de conformidade. Contraste deve ser medido para cada combinação de texto, fundo, estado selecionado, foco, botão e desabilitado. Em particular, validar o contraste do cartão Órbita em produção; a paleta do protótipo ainda requer revisão contrastiva integral.

### 7.2 Comportamento acessível

- Usar botão nativo para cada palavra e `aria-pressed` para comunicar a seleção.
- Não aplicar `role="grid"` sem implementar também o padrão de teclado esperado para grid ARIA. Um grupo de botões em fluxo DOM é adequado ao MVP.
- Permitir operação integral com `Tab`, `Shift+Tab`, `Enter` e `Espaço`.
- Manter foco visível e ordem de foco previsível; após re-renderização, restaurar o foco no controle equivalente, sem deslocamento inesperado.
- Fornecer rótulos e estados textuais para seleção, erro, acerto, progresso e modo; nunca comunicar apenas por cor.
- Usar regiões de status concisas com `aria-live="polite"` para anunciar confirmação, acerto, erro e pista; não reler o tabuleiro inteiro a cada atualização.
- Ao revelar categoria, anunciar a categoria e a quantidade de grupos restantes sem interromper a navegação.
- Alvos de toque devem buscar pelo menos 44 × 44 CSS px sempre que o layout permitir.
- Verificar reflow em viewport de 320 CSS px e ampliação de 200%, sem conteúdo ou controles cortados.
- Testar leitores de tela com combinações reais de navegador e leitor de tela nos ambientes suportados.
- Indicar instruções para seleção e confirmação fora da grade, em texto claro.

## 8. Persistência local e privacidade

### 8.1 Armazenamento

Salvar apenas o estado necessário para retomar a partida e as estatísticas que forem aprovadas. Não salvar dados pessoais. Sugestão de envelope:

```json
{
  "schemaVersion": 1,
  "savedAt": "ISO-8601",
  "game": {
    "puzzleId": "local-...",
    "contentVersion": 1,
    "mode": "relaxed",
    "status": "playing",
    "solvedGroupIds": [],
    "selectedWordIds": [],
    "mistakes": 0
  }
}
```

Antes de implementar, decidir se o puzzle e as respostas são persistidos integralmente ou se o `puzzleId` permite reconstruí-los com segurança. Como não há servidor nem sincronização, persistir somente um identificador não é suficiente se o catálogo correspondente deixar de existir na atualização.

### 8.2 Migração e erros

- Versionar o esquema salvo e validar dados ao ler.
- Ignorar estado malformado com mensagem clara de que a partida não pôde ser restaurada; oferecer nova partida.
- Tratar `QuotaExceededError`, modo de navegação privada e indisponibilidade de armazenamento sem impedir uma partida em memória.
- Se não puder salvar, informar que o progresso pode não sobreviver ao fechamento da página.
- Oferecer forma de apagar progresso local antes de considerar persistência/estatísticas completas.
- Não armazenar telemetria remota no MVP; se for adicionada, definir finalidade, consentimento, retenção e requisitos de privacidade aplicáveis.

LocalStorage não é cofre seguro: qualquer pessoa com acesso ao navegador pode inspecionar seus dados. Não guardar segredos, credenciais ou dados pessoais.

## 9. Compartilhamento sem spoilers

O compartilhamento deve ser iniciado somente por ação explícita e após construir uma prévia neutra. O texto não deve incluir palavras, nomes de categorias ainda não reveladas, respostas, chave interna, semente ou dado pessoal.

Fluxo sugerido:

1. Montar texto de resultado a partir de campos permitidos, como grupos resolvidos, erros e uma chamada para jogar.
2. Mostrar a prévia em diálogo acessível e permitir cancelar.
3. Se houver suporte, oferecer `navigator.share`; como alternativa, usar `navigator.clipboard.writeText`.
4. Se o clipboard falhar ou não estiver disponível, manter texto selecionável e explicar como copiar manualmente.
5. Anunciar sucesso ou falha em uma região de status.

**Pendente no PRD:** quais estatísticas são compartilhadas, se a partilha está disponível antes da conclusão e se haverá formato visual/grade. Não publicar esses detalhes como especificação final até decisão.

## 10. Interfaces entre módulos

As interfaces abaixo são contratos conceituais; adaptar os nomes ao estilo final do repositório:

```js
createPuzzle(content, options) -> Result<Puzzle, GenerationError>
validatePuzzle(puzzle, contentRules) -> ValidationResult
createGame(puzzle, mode) -> GameState
toggleWordSelection(state, wordId) -> GameState
submitSelection(state, rules) -> GameActionResult
requestHint(state, hintPolicy) -> GameActionResult
serializeGame(state) -> PersistedGame
restoreGame(serialized, contentCatalog) -> Result<GameState, RestoreError>
buildShareText(state, sharePolicy) -> string
```

Funções do domínio devem ser previsíveis e, sempre que possível, puras: recebem estado e entrada, retornam novo estado/resultado; não acessam `document`, `localStorage`, relógio global ou APIs de navegador diretamente. Os adaptadores de interface, persistência e compartilhamento são responsáveis por esses efeitos.

## 11. Tratamento de erros e observabilidade

| Situação | Comportamento esperado |
|---|---|
| Não há combinações de categorias válidas | Não exibir puzzle inválido; tentar fallback e então explicar que não foi possível iniciar uma partida no momento. |
| Estado salvo incompatível/corrompido | Não restaurar parcialmente; explicar e permitir começar novamente. |
| Armazenamento bloqueado | Continuar em memória, mostrar aviso de progresso não persistido. |
| Clipboard indisponível | Selecionar a prévia para cópia manual e orientar a pessoa. |
| Compartilhamento cancelado | Tratar cancelamento como ação do usuário, sem informar falso sucesso. |
| Erro inesperado de interface/domínio | Manter estado consistente, anunciar uma mensagem compreensível e registrar diagnóstico técnico sem dados pessoais. |

Diagnósticos no MVP devem permanecer locais ao console/desenvolvimento, sem coleta remota não aprovada. Mensagens técnicas não devem expor chaves internas ou respostas não descobertas na interface.

## 12. Estratégia de testes

### 12.1 Testes unitários de domínio

- Seleção adiciona/remove palavra e nunca excede quatro.
- Seleção não inclui palavras resolvidas ou inexistentes.
- Confirmação exige exatamente quatro palavras.
- Combinação correta revela o grupo certo e atualiza progresso.
- Combinação incorreta incrementa erros uma única vez e mantém grupos não resolvidos.
- A partida vence somente após resolver os quatro grupos.
- Modo relaxado nunca encerra por erros.
- Modo com limite não encerra até o limite final ser acordado e implementado.
- Gerador rejeita contagem incorreta, palavras duplicadas e conflitos de categoria.
- Gerador termina com falha explícita após o limite de tentativas.
- Normalização compara acentos/caixa segundo política aprovada sem alterar texto exibido.
- Texto compartilhado não contém resposta não descoberta.

### 12.2 Testes de integração e ponta a ponta

- Iniciar partida sem cadastro.
- Selecionar por mouse/toque e por teclado.
- Confirmar erro e acerto, incluindo mensagens e foco.
- Acessar pista conforme política configurada.
- Restaurar progresso após recarregar, limpar dados e tratar armazenamento bloqueado.
- Abrir/fechar diálogo de compartilhamento e testar sucesso e fallback de clipboard.
- Percorrer os três breakpoints: celular estreito, tablet e desktop.
- Verificar que nenhum estado depende somente de cor.
- Verificar que modo reduzido remove animação não essencial.

### 12.3 Revisão manual de acessibilidade

Automação é insuficiente para validar qualidade do jogo. Incluir testes manuais com teclado, zoom de 200%, leitor de tela e avaliação do contraste. Confirmar que leitores de tela recebem feedback sem anúncio excessivo e que a ordem visual corresponde à ordem de foco/leitura.

## 13. Segurança e confiabilidade

- Hospedar sob HTTPS em produção.
- Evitar dependências externas desnecessárias, especialmente fontes e scripts de terceiros.
- Não aceitar HTML arbitrário em categorias/palavras; inserir conteúdo como texto, escapando qualquer conteúdo de dados.
- Validar estrutura do catálogo e de dados armazenados antes de usar.
- Aplicar uma Content Security Policy compatível com a estratégia de build e sem `unsafe-eval` sempre que possível.
- Fixar e revisar versões de dependências caso sejam adicionadas.
- Como o MVP não envia respostas para uma API, não existe garantia de segredo contra inspeção local; isso é compatível com partidas individuais sem competição online, mas precisa ser reavaliado se houver ranking/desafio competitivo.
- Registrar autor e licença de bibliotecas, vocabulários e modelos usados na cadeia de conteúdo.

## 14. Implantação e operação

1. Gerar arquivos estáticos com build reproduzível.
2. Executar testes unitários, validação do catálogo, auditoria automatizada e teste de produção em navegador.
3. Publicar em hospedagem estática com HTTPS e cache apropriado.
4. Versionar catálogo e aplicação conjuntamente, permitindo invalidar cache de dados quando muda o formato.
5. Manter mecanismo para retirar uma versão de conteúdo problemática e publicar atualização.
6. Exibir versão da aplicação/conteúdo em diagnóstico ou ferramenta de suporte, sem expor dados da partida.

PWA/offline instalável pode ser uma evolução, não requisito implícito do MVP. Se for aprovada, o Service Worker deve versionar cache de interface e catálogo e oferecer atualização previsível, evitando restaurar progresso com formato incompatível.

## 15. Fases de implementação

### Fase 0 — Fechar decisões bloqueantes

- Definir limite e comportamento de erros do modo limitado.
- Definir gatilho, texto, quantidade e efeito das pistas.
- Aprovar conteúdo e formato de compartilhamento.
- Definir geração: catálogo estruturado, outra abordagem automática ou combinação.
- Definir fontes lexicais, licença, normalização, filtros e fallback para falhas.
- Confirmar critérios de acessibilidade do MVP e navegadores suportados.

### Fase 1 — Fundamentos

- Adicionar pipeline mínimo de build e testes, se aprovado.
- Separar domínio, conteúdo, renderização, armazenamento e compartilhamento.
- Implementar esquema e validação de conteúdo.
- Definir tokens e componentes Órbita com contraste verificado.

### Fase 2 — Loop principal

- Implementar seleção, confirmação, feedback, grupos resolvidos e condição de vitória.
- Implementar modos conforme regras aprovadas.
- Implementar pistas configuradas, sem valores provisórios.
- Implementar estado vazio, erro e retomada.

### Fase 3 — Acessibilidade, persistência e compartilhamento

- Finalizar operação por teclado e anúncios de status.
- Implementar persistência versionada e ação de apagar dados locais.
- Implementar prévia sem spoilers e fallback para partilha.
- Testar em dispositivos e combinações reais de navegador/leitor de tela.

### Fase 4 — Conteúdo e lançamento

- Validar o catálogo e procedência das licenças.
- Rodar testes com pessoas falantes de português brasileiro.
- Ajustar categorias ambíguas e dificuldade com evidências de uso consentidas.
- Revisar desempenho, acessibilidade e política de conteúdo antes da publicação.

## 16. Critérios técnicos de aceite

- O jogo funciona em navegadores modernos de celular e desktop sem conta.
- O motor apresenta exatamente 16 palavras únicas em quatro grupos completos.
- Puzzles inválidos são rejeitados antes de aparecer na interface.
- Uma pessoa consegue resolver os grupos com mouse, toque e teclado.
- Acerto revela a categoria imediatamente; erro comunica estado sem depender apenas de cor.
- As regras de limite e pista são implementadas somente com valores aprovados no PRD.
- Progresso local pode ser restaurado ou a falha de persistência é informada.
- Compartilhamento apresenta prévia e não inclui respostas proibidas pela política.
- Em largura de 320 CSS px e zoom de 200%, não há rolagem horizontal nem controles inutilizáveis.
- Foco permanece visível e previsível após acerto, erro, nova partida e fechamento do diálogo.
- `prefers-reduced-motion` é respeitado.
- Não há coleta remota de dados pessoais no MVP.

## 17. Riscos técnicos e decisões restantes

| Tema | Risco/decisão |
|---|---|
| Geração automática | Combinação de conteúdo conhecido pode não garantir clareza semântica; definir regras e estratégia de fallback. |
| Fonte lexical | Licença pode impedir redistribuição; validar antes de incluir corpus no bundle. |
| Persistência | Atualizações podem tornar uma partida salva ilegível; definir migração e política de descarte. |
| Privacidade do estado | LocalStorage é inspecionável pelo usuário; não usar como armazenamento secreto. |
| Compartilhamento | API varia por navegador e permissões; manter fluxo manual acessível. |
| Tema Órbita | Contraste da paleta do protótipo ainda precisa de auditoria completa. |
| Modo limitado | Limite e efeito ao atingir permanecem bloqueantes para implementação final. |
| Pistas | Gatilho, quantidade e efeito no resultado ainda não foram acordados. |
| Sem servidor | Não há prevenção global de repetição, atualização remota ou métricas entre dispositivos; confirmar se isso atende ao MVP. |
| Marca | “Órbita” é somente nome do conceito visual; nome de produto e disponibilidade de marca continuam fora do escopo técnico aprovado. |

## 18. Referências de implementação e padrões

- [PRD do produto](./PRD.md)
- [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [WCAG 2.2 — W3C](https://www.w3.org/TR/WCAG22/)
- [MDN — Web Share API](https://developer.mozilla.org/docs/Web/API/Web_Share_API)
- [MDN — Clipboard API](https://developer.mozilla.org/docs/Web/API/Clipboard_API)
- [MDN — Web Storage API](https://developer.mozilla.org/docs/Web/API/Web_Storage_API)
