# PRD — Jogo casual de agrupamento de palavras

**Status:** rascunho inicial  
**Versão:** 0.1  
**Data:** 14 de outubro de 2026

## 1. Visão geral

Criar um jogo casual para web em que a pessoa encontra relações entre palavras e as organiza em grupos. A experiência deve ser simples de iniciar, funcionar em celular e computador e permitir partidas ilimitadas. Os puzzles serão gerados automaticamente e passarão por validações antes de serem apresentados.

## 2. Problema e oportunidade

Jogos de agrupamento oferecem um desafio curto baseado em reconhecer padrões e testar hipóteses. Para que a experiência pareça justa, as relações entre as palavras precisam ser compreensíveis e as categorias não devem parecer arbitrárias. O produto deve combinar uma entrada acessível com opções de desafio e comunicar claramente o resultado de cada seleção.

## 3. Público e plataformas

- **Público inicial:** pessoas interessadas em jogos casuais de palavras; segmentos e faixa etária ainda a definir.
- **Idioma e localização:** português brasileiro, com vocabulário e categorias adequados ao público-alvo.
- **Plataforma:** aplicação web responsiva para celular e computador.
- **Conta:** não exigir cadastro no MVP.

## 4. Objetivos do produto

1. Permitir que a pessoa inicie uma partida rapidamente e entenda a mecânica sem explicações extensas.
2. Oferecer puzzles de agrupamento claros, variados e rejogáveis.
3. Permitir escolher entre uma experiência relaxada e uma com limite de erros.
4. Permitir compartilhar o resultado sem revelar as respostas.
5. Validar a diversão e a qualidade dos puzzles antes de considerar monetização ou funcionalidades sociais mais amplas.

### Não objetivos do MVP

- Desafio diário ou calendário global de puzzles.
- Jogo cooperativo, partidas síncronas ou desafios diretos entre amigos.
- Conta obrigatória, sincronização entre dispositivos ou rankings online.
- Anúncios, assinatura ou compras.
- Progressão obrigatória de dificuldade.
- Conteúdo criado pela comunidade.

## 5. Experiência principal

1. A pessoa abre o jogo e inicia uma partida.
2. Escolhe o modo relaxado ou o modo com limite de erros.
3. Recebe 16 palavras apresentadas em uma grade.
4. Seleciona quatro palavras que acredita pertencerem à mesma categoria e confirma a seleção.
5. Se o grupo estiver correto, as quatro palavras saem da grade e o nome da categoria é revelado imediatamente.
6. Se estiver incorreto, o jogo informa o erro de acordo com o modo escolhido. A quantidade permitida de erros e o efeito de atingir o limite ainda precisam ser definidos.
7. A pessoa pode usar uma pista opcional após algum progresso. O gatilho e o conteúdo da pista ainda precisam ser especificados.
8. A rodada termina quando os quatro grupos forem resolvidos ou quando a regra do modo com limite de erros determinar o encerramento.
9. O jogo apresenta o resultado e oferece compartilhamento sem spoilers. A pessoa pode iniciar outra partida sem esperar por um novo dia.

## 6. Requisitos funcionais

### RF-01 — Iniciar partida

- O sistema deve permitir iniciar uma nova partida sem conta e sem aguardar um horário ou calendário diário.
- O sistema deve oferecer os modos relaxado e com limite de erros.
- A regra de geração de partidas repetidas e prevenção de puzzles duplicados está pendente.

### RF-02 — Apresentar o puzzle

- Cada puzzle deve conter 16 palavras distribuídas em uma grade.
- Cada puzzle deve ter quatro categorias, cada uma com exatamente quatro palavras.
- A seleção deve permitir marcar palavras e confirmar um grupo de quatro.
- O estado de seleção deve ser visível e a pessoa deve poder alterar a seleção antes de confirmá-la.

### RF-03 — Avaliar grupos

- Uma tentativa com quatro palavras deve ser avaliada como correta ou incorreta.
- Ao acertar, o sistema deve remover essas palavras da grade e revelar imediatamente o nome da categoria.
- Ao errar, o sistema deve comunicar o resultado e aplicar a regra de erros correspondente ao modo.
- O sistema não deve revelar a resposta de um grupo não resolvido antes do fim da rodada, salvo pela regra final que vier a ser definida.

### RF-04 — Modo relaxado

- O modo relaxado não deve encerrar a partida por quantidade de erros.
- O tratamento de pontuação, registro de erros e uso de pistas neste modo está pendente.

### RF-05 — Modo com limite de erros

- O modo deve contabilizar tentativas incorretas e encerrar ou alterar a partida ao atingir um limite.
- **Pendente de decisão:** valor do limite, se tentativas incorretas consecutivas importam, comportamento ao atingir o limite e revelação das categorias restantes.

### RF-06 — Pistas

- O sistema deve disponibilizar uma pista opcional após algum progresso na rodada.
- A pista não deve ser obrigatória para concluir a partida.
- **Pendente de decisão:** qual progresso habilita a pista, quantidade de pistas por rodada, formato da pista e se seu uso afeta pontuação ou compartilhamento.

### RF-07 — Resultado e compartilhamento

- Ao terminar uma partida, o sistema deve apresentar um resumo do resultado.
- A pessoa deve poder compartilhar esse resumo sem expor as palavras ou categorias ainda não descobertas.
- O compartilhamento deve ser opcional.
- **Pendente de decisão:** conteúdo exato do resumo e formato de compartilhamento (por exemplo, texto copiável ou imagem).

### RF-08 — Salvamento local

- O sistema deve salvar o progresso e as estatísticas apenas no dispositivo, sem exigir uma conta.
- **Pendente de decisão:** quais dados persistem, por quanto tempo, como retomar uma partida interrompida e como apagar os dados locais.

### RF-09 — Geração e validação de puzzles

- Os puzzles devem ser gerados automaticamente, sem revisão manual antes da publicação.
- Um puzzle só deve ser oferecido se as validações automáticas de unicidade, clareza da relação, dificuldade e vocabulário forem aprovadas.
- As validações devem verificar pelo menos:
  - 16 palavras distintas e quatro grupos com quatro palavras cada;
  - ausência de palavras inválidas ou fora do vocabulário suportado;
  - ausência de duplicação em relação ao histórico armazenado, quando esse histórico estiver disponível;
  - consistência entre a resposta gerada e os quatro grupos esperados.
- **Pendente de especificação técnica:** método para estimar clareza e dificuldade, fontes lexicais licenciadas, regras para acentos/flexões/variantes e ação do sistema quando um puzzle falhar na validação.
- A validação automática reduz riscos, mas não garante que toda associação seja percebida como justa. A qualidade percebida precisa ser monitorada após o lançamento.

### RF-10 — Política de conteúdo

- O conteúdo não deve incluir discurso de ódio nem material explícito.
- Outros temas não serão excluídos automaticamente no MVP.
- **Pendente de decisão:** processo para reportar um puzzle problemático e política de revisão/retirada após publicação.

## 7. Requisitos não funcionais

### Acessibilidade e usabilidade

Como requisito proposto para o MVP, e sujeito à confirmação:

- Não depender exclusivamente de cor para comunicar seleção, acerto ou erro.
- Oferecer contraste adequado, foco visível e operação por teclado.
- Usar alvos de toque apropriados em dispositivos móveis.
- Fornecer nomes e estados acessíveis aos leitores de tela.
- Manter layout e texto legíveis em telas pequenas e com zoom.

### Privacidade

- Não exigir conta nem coletar dados pessoais para jogar no MVP.
- Manter progresso e estatísticas localmente, conforme o requisito de salvamento definido.
- Se forem adicionados analytics ou serviços externos, documentar dados coletados, finalidade, retenção e consentimento aplicável antes do lançamento.

### Compatibilidade e desempenho

- A interface deve ser responsiva em navegadores modernos para celular e computador.
- Metas de navegadores suportados, desempenho e disponibilidade ainda precisam ser definidas.

### Propriedade intelectual e licenças

- Usar nome, identidade visual, interface, textos, lista de palavras e conteúdo próprios ou devidamente licenciados.
- Registrar a procedência e as condições de uso das fontes lexicais e de quaisquer ferramentas ou modelos de geração.
- Não reutilizar listas editoriais, respostas, calendário, assets ou código de outros jogos sem autorização.
- Pesquisar disponibilidade de nome e marca nos mercados pretendidos antes do lançamento. Esta seção é orientação de produto, não aconselhamento jurídico.

## 8. Métricas de sucesso

Instrumentar métricas agregadas apenas se a solução de analytics e privacidade for definida. Métricas candidatas:

- taxa de conclusão por puzzle e por modo;
- erros médios por rodada;
- abandono por etapa da partida;
- frequência de uso de pistas e conclusão após a pista;
- partidas iniciadas e concluídas por sessão;
- retorno D1, D7 e D30;
- compartilhamentos iniciados;
- reclamações ou relatos de ambiguidade, vocabulário inadequado ou dificuldade injusta.

As métricas são propostas para avaliação, não metas já acordadas. Metas quantitativas e a forma de coleta devem ser definidas antes de um teste com usuários.

## 9. Riscos e mitigação

| Risco | Mitigação proposta |
|---|---|
| Relações ambíguas ou respostas que pareçam arbitrárias | Validações automáticas, explicação da categoria ao acertar e monitoramento de abandono/relatos por puzzle. |
| Geração automática publicar palavras impróprias ou inadequadas ao português brasileiro | Fonte lexical licenciada, regras de vocabulário, filtros de conteúdo e mecanismo de retirada/report. |
| Dificuldade irregular | Acompanhar métricas por puzzle e ajustar os critérios automáticos com base nos resultados observados. |
| Repetição de puzzles em partidas ilimitadas | Armazenar identificadores/histórico local ou manter um catálogo de puzzles; capacidade e prevenção entre dispositivos ainda não definidas. |
| Pista reduzir demais o desafio ou afetar comparação | Definir o conteúdo e o efeito da pista no resumo compartilhado antes de implementar competição ou pontuação. |
| Validação automática não reconhecer ambiguidade semântica | Tratar aprovação automática como filtro mínimo, não como garantia de qualidade; avaliar sinais de experiência e criar um fluxo de correção. |

## 10. Decisões pendentes

1. Qual é o limite de erros no modo com limite e o que acontece quando ele é atingido?
2. O que conta como “progresso” para habilitar a pista, e qual informação ela revela?
3. Como serão gerados os puzzles: algoritmo, modelo, catálogo ou combinação? Quais fontes e licenças serão usadas?
4. Como definir e validar automaticamente clareza e dificuldade de uma categoria?
5. Como impedir repetições para quem joga muitas partidas, sem conta e em um único dispositivo?
6. Que estatísticas serão salvas localmente e como a pessoa poderá apagá-las?
7. O que será incluído no resultado compartilhado?
8. Haverá pontuação, classificação ou apenas conclusão da rodada?
9. Quais navegadores e metas de desempenho o MVP deve suportar?
10. Qual é o público-alvo prioritário e há requisitos específicos de faixa etária?
11. Como a pessoa poderá reportar conteúdo problemático?
12. As salvaguardas de acessibilidade propostas serão critérios obrigatórios de aceite do MVP?

## 11. Critérios de aceite do MVP

- Uma pessoa pode iniciar uma partida sem criar conta.
- O jogo oferece os modos relaxado e com limite de erros.
- Um puzzle apresenta 16 palavras, organizadas em quatro grupos de quatro.
- A pessoa pode selecionar e confirmar quatro palavras.
- Um grupo correto é retirado da grade e sua categoria é revelada imediatamente.
- Um erro é comunicado e contabilizado conforme as regras específicas de cada modo, após definir os parâmetros pendentes.
- Uma pista opcional fica disponível após o gatilho definido.
- Uma partida pode ser concluída e seu resultado compartilhado sem revelar respostas não descobertas.
- O progresso definido para o MVP é persistido localmente e pode ser apagado.
- Puzzles que falham nas validações automáticas não são apresentados ao jogador.
- O conteúdo segue a política acordada para discurso de ódio e material explícito.
- A experiência funciona em telas de celular e computador; os critérios de acessibilidade propostos são confirmados antes de serem considerados obrigatórios.

## 12. Referências de pesquisa

As páginas de produto abaixo servem como referências de mecânicas, não como especificação a copiar:

- [Wordle — The New York Times](https://www.nytimes.com/games/wordle)
- [Connections — The New York Times](https://www.nytimes.com/games/connections)
- [Strands — The New York Times](https://www.nytimes.com/games/strands)
- [Quordle — Merriam-Webster](https://www.merriam-webster.com/games/quordle/)
- [Contexto](https://contexto.me/)
- [Semantle](https://semantle.com/)
- [Cémantix](https://cemantix.certitudes.org/)
- [Codenames Online](https://codenames.game/)
- [WCAG 2.2 — W3C](https://www.w3.org/TR/WCAG22/)
- [Estudo sobre desempenho e dificuldade no Wordle — arXiv](https://arxiv.org/abs/2311.16777)
- [Revisão de estudos empíricos sobre gamificação — HICSS](https://doi.org/10.1109/HICSS.2014.377)
- [U.S. Copyright Office — Circular 33](https://www.copyright.gov/circs/circ33.pdf)
- [USPTO — Trademark basics](https://www.uspto.gov/trademarks/basics)
- [Termos de serviço do The New York Times](https://help.nytimes.com/policies/115014893428-Terms-of-Service)
