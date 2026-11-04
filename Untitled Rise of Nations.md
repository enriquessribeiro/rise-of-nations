# Untitled: Rise of Nations

**Game Design Document — GDD · Versão 1.2**

**Plataforma:** Roblox · **Gênero:** Estratégia grand strategy multiplayer · **Referências:** Victoria 3, Age of History, Territorial.io, OpenFront e Risk.

> **Sobre esta versão:** incorpora as propostas de balanceamento e regras de borda discutidas após a v1.1 e as alterações de identidade: **sem bandeiras e sem nomes de países**. A nação é identificada pelo **nome do jogador** e as províncias recebem **nomes aleatórios**. Todos os números são **valores iniciais de teste**, centralizados em um ModuleScript `Config`.

---

## 1. Visão geral

Untitled: Rise of Nations é um jogo de estratégia multiplayer em tempo real no qual os jogadores comandam nações em um mundo fictício.

Cada jogador começa com uma província inicial e precisa desenvolver sua economia, administrar recursos, expandir suas fronteiras, estabelecer relações diplomáticas e disputar territórios com outras nações.

O jogo é inspirado em grand strategy, mas com sistemas significativamente simplificados para proporcionar partidas mais rápidas e acessíveis.

**Objetivo principal:** construir a nação mais poderosa da partida por meio de desenvolvimento econômico, expansão territorial, diplomacia e força militar.

### 1.1 Diretriz central de design

As nações controladas pelos jogadores humanos são o foco da experiência. As nações-bots complementam o cenário geopolítico, criam oportunidades de interação e proporcionam desafios, mas não devem dominar a experiência nem prejudicar o desenvolvimento dos humanos.

---

## 2. Estrutura das partidas

- Duração média pretendida: **45 minutos**.
- Quantidade inicial: de 2 a 4 jogadores humanos por servidor.
- Objetivo futuro: 8 a 12 jogadores humanos.
- Mundo fictício dividido em províncias.
- Cada partida começa com um novo mundo (biomas, recursos e nomes de províncias sorteados).
- O mapa conta com nações controladas por inteligência artificial (bots).

### 2.1 Matchmaking e lobby

- Lobby com contagem de **60 s**; mínimo de **2 humanos** e máximo de 4.
- Vagas humanas vazias não são ocupadas por jogadores: o território correspondente é distribuído entre bots.

### 2.2 Entrada no meio da partida

- Permitida nos **3 primeiros minutos**, se houver capital livre.
- Depois disso, o jogador só pode assumir uma **nação-bot pequena**, e apenas até os **15:00**.
- Após os 15:00, novos jogadores entram como espectadores.

### 2.3 Desconexão e AFK

- Ao desconectar (ou ficar inativo), a nação entra em **piloto automático**: comportamento de bot defensivo, sem expandir.
- O jogador pode reassumir o controle ao voltar.
- O piloto automático também cobre o caso de um líder AFK.

---

## 3. Sistema de tempo

O jogo usa um relógio global, contínuo e sincronizado.

**Regra de velocidade: 1 ano de jogo = 60 segundos reais (1 mês = 5 segundos).**

> *Correção da v1.1:* a regra anterior (1 s = 1 dia) fazia 45 minutos equivalerem a apenas \~7,4 anos, e não 45. Com a nova regra, 45 minutos = 45 anos.

- Todos os jogadores compartilham a mesma passagem de tempo.
- Não é possível acelerar, desacelerar ou pausar individualmente.
- A velocidade é fixa durante toda a partida.
- O servidor controla o relógio e distribui as atualizações.
- **Ciclo econômico = 1 mês de jogo = 5 segundos**: produção, consumo e manutenção são processados a cada ciclo, evitando picos e excesso de notificações.

---

## 4. Mundo e territórios

O mapa é inteiramente fictício, dividido em províncias e regiões geográficas.

**Escopo inicial (MVP):**

- 30 províncias continentais, em 3 continentes de 10 províncias cada.
- Até 4 jogadores humanos.
- Cada jogador começa com uma província que funciona como capital.
- Não há países predefinidos nem bandeiras: a nação é identificada pelo **nome do jogador** e por uma **cor** (ver 4.7).
- O mapa é ocupado por nações de jogadores e nações bots.
- Ilhas e arquipélagos adicionais ficam reservados para futuras mecânicas marítimas.

O mapa é apresentado em uma interface estratégica 2D, com seleção de províncias, visualização de fronteiras e informações territoriais.

### 4.1 Expansão territorial

- Uma nação pode atacar províncias neutras que façam fronteira terrestre com pelo menos uma província sua.
- Pode atacar províncias de outras nações, respeitando as regras de guerra (seção 8.8).
- Províncias sem conexão terrestre registrada não podem ser conquistadas pelo sistema convencional.
- A conquista depende do resultado do combate.
- **Anti-snowball:** o custo de expansão aumenta **+8% por província** acima de 5.
- Mecanismos diplomáticos também podem permitir a transferência de territórios.

### 4.2 Expansão marítima futura

A arquitetura territorial deve permitir expansão marítima em versões posteriores (colonização de províncias costeiras distantes, portos, rotas marítimas, disputas por ilhas), com requisitos econômicos, tecnológicos ou militares. **Não faz parte do MVP.**

### 4.3 Distribuição territorial inicial

O mapa não começa vazio.

**Posicionamento dos jogadores humanos:**

1. O mapa completo é apresentado aos jogadores.
2. Cada jogador explora os continentes e seleciona uma província disponível.
3. Ao selecionar, são exibidos nome, características geográficas, recursos e potencial econômico.
4. O jogador confirma a escolha e funda sua nação.
5. O servidor valida a escolha, impedindo que dois jogadores ocupem a mesma província.
6. A província se torna a capital da nação.

**Recomendações dinâmicas:** o sistema analisa a proximidade entre as capitais já escolhidas e pode destacar províncias que favoreçam uma distribuição equilibrada. As recomendações são **informativas, não restrições**.

**Distribuição das nações-bots:** ocorre somente depois que todos os humanos escolherem suas capitais.

- As províncias continentais restantes são distribuídas entre bots.
- A quantidade de bots depende da distribuição territorial gerada.
- Os bots têm tamanhos variados (pequenos, médios e grandes), sem concentração excessiva em uma única nação.
- A geração prioriza as posições escolhidas pelos humanos e suas oportunidades de expansão.

### 4.4 Proteção de expansão inicial dos jogadores

**Objetivo:** garantir espaço inicial para expansão e evitar que bots ocupem todas as províncias próximas às capitais humanas.

- Após a escolha das capitais, o sistema identifica áreas de expansão próximas a cada uma, calculadas por geografia e adjacência, com oportunidades equivalentes entre os jogadores.
- Durante a distribuição inicial, as províncias reservadas não podem ser ocupadas por bots e permanecem **neutras**.
- A proteção dura **3 minutos reais após o início da partida**. Nesse período, bots não podem reivindicar nem atacar as províncias protegidas.
- Os humanos conquistam essas províncias normalmente; a proteção não concede propriedade automática.
- Após os 3 minutos, os bots podem disputar as que ainda estiverem neutras.
- A proteção não impede a competição entre humanos nem cria vantagens permanentes.

### 4.5 Geografia e recursos — sistema híbrido

**Características individuais:** cada província tem sua própria distribuição de recursos (terras férteis, montanhas, florestas, costa). Vizinhas podem ter potenciais diferentes.

**Características regionais:** cada região oferece pequenos bônus (+10%) a certas atividades: montanhosa para mineração, florestal para madeira, planícies para agricultura, costeira para comércio.

Os bônus regionais são moderados, para que os recursos de cada província continuem importantes, incentivando diferentes estratégias sem criar regiões obrigatoriamente superiores.

### 4.6 Organização dos continentes

Três continentes separados por oceanos, com 10 províncias cada (30 no MVP), sem vantagens absolutas entre si.

| Continente | Característica predominante | Principal potencial |
| --- | --- | --- |
| Continente 1 | Planícies | Agricultura e alimentos |
| Continente 2 | Montanhas | Mineração e matérias-primas |
| Continente 3 | Florestas | Madeira e recursos naturais |

**Intensidade:** a especialização é moderada. Todos os continentes têm diversidade de terrenos e recursos, e nenhum depende exclusivamente de outro.

**Organização territorial:**

- Estruturas assimétricas, com fronteiras irregulares, gargalos e passagens estratégicas.
- Mesmo número de províncias por continente, com configurações diferentes.
- **No MVP, os 3 continentes são fixos e desenhados à mão.** O que varia por partida são biomas, recursos e nomes, sorteados por seed e regras. Geração procedural do formato fica para depois.

**Modelo de divisão:** contornos naturais, províncias poligonais, estrutura lógica por trás do mapa e conexões territoriais registradas individualmente. **Proximidade visual não basta: é preciso haver conexão registrada.**

**Ilhas e arquipélagos:** províncias adicionais, fora das 30 continentais; neutras e inacessíveis no MVP; fora da distribuição inicial; reservadas para colonização, portos e rotas futuras.

### 4.7 Identidade das nações e nomes das províncias *(novo)*

**Nações dos jogadores:**

- **Não há criação de nome de país nem de bandeira.** A nação é identificada pelo **nome do jogador** (DisplayName do Roblox, já filtrado pela plataforma).
- O jogador escolhe apenas a **cor** da nação, entre uma paleta predefinida. Se a cor já estiver em uso, o servidor oferece a próxima disponível.
- Isso elimina a necessidade de filtro de texto próprio e de moderação de imagens.

**Nações-bots:** recebem um nome gerado aleatoriamente a partir de um conjunto de nomes próprios fictícios (único por partida) e uma cor atribuída automaticamente, sem repetir as dos humanos.

**Províncias:**

- Cada província recebe um **nome aleatório**, gerado por sorteio de sílabas e/ou de uma lista de nomes fictícios.
- Os nomes são **únicos dentro da partida** e sorteados por seed no início, ficando fixos até o fim.
- Como são gerados pelo servidor a partir de listas internas, não precisam de filtro de texto.
- O nome da província aparece no painel lateral, na seleção de capital, nos alertas de combate e na lista de ataques.
- Nomes não carregam significado geográfico: o bioma é indicado por ícone e cor, não pelo nome.

---

## 5. Economia

O sistema econômico tem **dinheiro e três recursos**, processados a cada ciclo de 5 segundos (1 mês de jogo).

| Recurso | Utilização |
| --- | --- |
| Dinheiro | Construções, expansão, recrutamento e manutenção |
| Alimentos | Sustento da população e das tropas |
| Matérias-primas | Recrutamento, equipamentos e fábricas |
| Madeira | Construção, infraestrutura e fortificações |

### 5.1 Edificações do MVP

| Edifício | Função | Produção base por ciclo |
| --- | --- | --- |
| Fazenda | Produz alimentos | 10 alimentos |
| Mina | Produz matérias-primas | 8 matérias-primas |
| Serraria | Produz madeira | 8 madeira |
| Mercado | Gera receita comercial | 12 dinheiro |
| Quartel | Habilita e melhora o recrutamento | — |

**Fora do MVP:** Fábrica e Porto.

**Multiplicadores:** o recurso da província (0,5x a 1,5x) e o bônus regional (+10%). A disponibilidade e a produtividade dependem das características de cada província.

### 5.2 Consumo e manutenção

- **População:** 1 alimento por 1.000 habitantes por ciclo.
- **Tropas:** 1 alimento e 1 dinheiro a cada 500 tropas por ciclo.
- **Imposto:** gera dinheiro proporcional à população.
- **Fome:** reduz a estabilidade e, depois, a produção.

O consumo cria a tensão central do jogo: expandir e manter exércitos grandes exige sustentação econômica.

### 5.3 Construção

- **3 slots** de construção por província e **4 na capital**.
- Custo base: **100 dinheiro + 50 madeira**, com **15 s** de construção.
- **Upgrades de nível 1 a 3:** produção ×1,5 por nível e custo ×2.
- Fortificações consomem madeira (ver 8.3).

---

## 6. População e desenvolvimento

A população é representada por um valor agregado por província, sem classes sociais ou profissões. Ela influencia:

- Capacidade econômica e receita tributária.
- Crescimento populacional.
- Capacidade de recrutamento militar.
- Tamanho da guarnição da província (≈5% da população, ver 8.3).

A **estabilidade** é afetada por falta de alimentos, impostos elevados e ocupação recente. Província recém-conquistada fica **60 s instável, com produção −50%**.

---

## 7. Diplomacia (mínima no MVP)

O MVP inclui três acordos simples, registrados e validados pelo servidor. Cada proposta **expira em 30 s** se não for respondida.

| Acordo | Regras |
| --- | --- |
| Pacto de não agressão | Dura 3 min. Bloqueia a declaração de guerra entre as partes. Quebrar custa reputação. |
| Aliança | Limite de **1 aliado**. Compartilha a visão do mapa; os aliados não podem se atacar. Quebrar custa reputação. |
| Paz | Encerra a guerra e ativa o cooldown da seção 8.8. |

**Reputação (0 a 100):** quebrar um acordo dá **−30**. A reputação é visível a todos e os bots a usam em suas decisões.

**Aliados não vencem juntos**, o que preserva a traição como parte do jogo.

**Fora do MVP:** acordos comerciais, embargos e transferência de territórios por acordo.

---

## 8. Sistema militar e guerras

O combate é estratégico e automatizado, inspirado em sistemas de combate contínuo como o OpenFront, sem controle direto de unidades em tempo real. O jogador equilibra a capacidade de expansão e a necessidade de proteger seus territórios.

### 8.1 Reserva militar compartilhada

- Cada nação tem uma **reserva nacional de tropas**, compartilhada entre seus territórios.
- O jogador seleciona uma província adjacente e envia uma quantidade determinada de tropas.
- As tropas enviadas ficam **comprometidas** com o ataque e não defendem outras regiões enquanto estiverem nele.
- A quantidade mantida na reserva é uma decisão estratégica.
- Limite de **3 ataques simultâneos** por nação.

### 8.2 Sistema de ataque contínuo

1. O jogador seleciona uma província neutra ou de outra nação adjacente ao seu território.
2. Define a quantidade de tropas.
3. O servidor valida a operação (adjacência, posse, tropas, limite de ataques, estado de guerra).
4. As tropas avançam e combatem; as forças sofrem baixas progressivas.
5. Se a defesa cair a zero, a província é conquistada.
6. Se o ataque cair a zero, ele é encerrado.

As tropas comprometidas não podem ser usadas em outras operações.

### 8.3 Combate, baixas e defesa

O servidor processa o combate continuamente (simulação a 1 Hz).

**Fórmula base, por segundo:**

- `Baixas do defensor = Atacantes × 0,02 × mod_ataque`
- `Baixas do atacante = Defensores × 0,02 × mod_defesa`

**Modificadores de defesa:**

| Terreno | Modificador |
| --- | --- |
| Planície | 1,0 |
| Floresta | 1,15 |
| Montanha | 1,3 |

Somam-se **+10% por nível de fortificação** (máximo 3 níveis, construídas com madeira). Equipamentos e tecnologia entram como modificadores futuros.

**Defesa mínima (evita a reserva vazia):**

- Cada província tem uma **guarnição** (≈5% da população) que regenera sozinha e **não consome a reserva**.
- Além disso, até **25% da reserva** reforça automaticamente a província atacada, a 5% por segundo.

**Casos de borda:**

- **Dois atacantes na mesma província:** cada um luta contra a guarnição e eles não lutam entre si. Quem zerar a defesa primeiro conquista.
- **Ataques em direções opostas** são independentes.
- Os valores finais de dano e velocidade serão ajustados no balanceamento.

### 8.4 Retirada de ataques

O jogador pode interromper um ataque a qualquer momento, com custo militar.

**Regra: perda de 10% das tropas restantes no ataque.**

- Os 90% restantes retornam à reserva.
- O cálculo usa as tropas restantes no momento do cancelamento, não as originalmente enviadas.

| Exemplo | Quantidade |
| --- | --: |
| Tropas antes da retirada | 6.000 |
| Tropas perdidas (10%) | 600 |
| Tropas recuperadas (90%) | 5.400 |

### 8.5 Recuperação e recrutamento de tropas

Modelo híbrido:

- **Regeneração natural:** gradual e relativamente lenta, sem intervenção do jogador.
- **Recrutamento econômico:** o jogador investe dinheiro e matérias-primas para repor tropas mais rápido. Depende da população, da economia e do **Quartel**, que habilita e melhora o recrutamento.
- **Anti-snowball:** nação com menos de 50% da população média ganha **+25% de regeneração**.

A velocidade de regeneração, os custos e os limites de tropas serão definidos no balanceamento.

### 8.6 Crescimento militar

A quantidade de tropas acompanha o desenvolvimento: população, economia e Quartéis aumentam a capacidade de recrutamento. Tecnologias futuras poderão melhorar a eficiência militar.

### 8.7 Diretrizes estratégicas

- Enviar muitas tropas deixa a nação vulnerável.
- Manter reserva permite responder a ameaças.
- Retirar tropas é possível, mas custa perdas.
- Investir em recuperação militar compete com a economia.
- Ataques sucessivos sem recuperação enfraquecem a nação.

### 8.8 Guerra, ocupação e eliminação

**Declaração de guerra:**

- Atacar outra nação exige **declarar guerra**, com **20 s de aviso**.
- Após a paz, há **cooldown de 90 s** antes de nova declaração.
- Pacto de não agressão e aliança bloqueiam a declaração.
- Províncias neutras **não** exigem declaração.

**Perda da capital:** a capital não elimina a nação. Ela é realocada para a **província mais populosa**, com perda de **30% das tropas** e **25% do dinheiro**.

**Eliminação:** só é eliminado quem fica com **zero províncias**. O jogador eliminado tem **um respawn** ("governo no exílio") após **90 s**, em uma província rebelde ou neutra, com tropas iniciais. Depois disso, vira espectador.

---

## 9. Nações controladas por bots

As nações-bots ocupam as províncias não atribuídas aos humanos. São elementos complementares: o foco é a competição entre humanos. Devem enriquecer o cenário sem sufocar o crescimento dos jogadores.

### 9.1 Personalidades

- **Pacíficas:** priorizam economia, desenvolvimento e diplomacia.
- **Expansionistas:** buscam território e aceitam mais conflitos.
- **Comerciais:** priorizam riqueza, produção e acordos.

### 9.2 Distribuição e tamanho

Os bots começam com tamanhos variados (pequenos, médios e grandes), considerando o número de humanos, as províncias disponíveis, a distribuição entre continentes, o equilíbrio territorial e as áreas protegidas. Os tamanhos exatos serão definidos depois.

### 9.3 Tomada de decisão

- **Máquina de estados** com 4 estados: **Desenvolver, Expandir, Defender e Guerra**.
- Reavaliada a cada **5 s** por pontuação; cada personalidade só altera os pesos.
- Os bots respeitam as regras militares gerais (tropas comprometidas, baixas e recuperação).

### 9.4 Limites contra jogadores humanos

- **Nenhum ataque antes dos 3:00.**
- Só declaram guerra a um humano com tropas **≥ 1,3x** as dele e sem proteção ativa.
- Máximo de **1 bot em guerra com o mesmo humano** por vez.
- **Cooldown de 90 s** entre ataques ao mesmo humano.
- **Rubber-band:** se um humano perdeu 2 ou mais províncias para bots em 2 minutos, os bots entram em paz com ele por 2 minutos.
- Nação com mais de **35% do Score** vira alvo preferencial dos bots.

### 9.5 Reação humana

- Atraso de **3 a 8 s** por decisão.
- Nunca enviam mais de **70% da reserva** em um ataque.

---

## 10. Tecnologia

O jogo poderá ter futuramente três áreas de pesquisa: Economia, Militar e Infraestrutura. A árvore tecnológica completa **não** faz parte da primeira versão jogável.

---

## 11. Interface e UX

A interface principal tem:

- Mapa estratégico central.
- Barra superior com data, dinheiro e recursos.
- Painel lateral com informações da província selecionada (nome aleatório, bioma, recursos, dono).
- Abas de território, economia, diplomacia e militar.

A tela de seleção inicial permite explorar o mapa, consultar as características das províncias, ver as recomendações de posicionamento, escolher a cor e confirmar a fundação da nação.

**Interface militar:** consultar a reserva, selecionar a província-alvo, definir tropas, acompanhar ataques e baixas, interromper ataques vendo as perdas da retirada, e acompanhar regeneração e recrutamento.

### 11.1 Mobile-first

- Botões com no mínimo **48 px**.
- Painel de província como ***bottom sheet***.
- Legibilidade do mapa em telas pequenas.

### 11.2 Tutorial

Partida guiada de **3 minutos** com 5 passos: escolher a capital, construir, atacar uma província neutra, retirar um ataque e propor um acordo diplomático.

### 11.3 Feedback de combate

- Barra de **"cabo de guerra"** na província em disputa.
- Setas animadas de avanço e números flutuantes de baixas.

### 11.4 Alertas de ataque

- Banner vermelho, som, província piscando e vibração no mobile.
- Esse alerta tem **prioridade máxima** na fila de notificações.

---

## 12. Condições de vitória e ritmo da partida

### 12.1 Poder Nacional (Score)

`Score = 50% × share de províncias + 25% × share de renda + 25% × share de tropas`

onde *share* é a fração da nação sobre o total do mapa.

### 12.2 Vitória

- **Ao fim dos 45:00:** vence o maior Score.
- **Vitória antecipada:** controlar **18 de 30 províncias (60%) por 2 minutos seguidos**.
- **Desempate:** mais províncias e, depois, maior renda.

### 12.3 Fases da partida

| Tempo | Fase |
| --- | --- |
| 0:00 a 3:00 | Proteção inicial |
| 3:00 a 20:00 | Expansão |
| 20:00 | Evento global "Crise de Suprimentos": consumo +25% por 3 min |
| 35:00 em diante | "Corrida Final": peso de províncias no Score sobe para 65% |

O objetivo dessas fases é forçar conflito entre humanos mesmo quando o mapa já estiver ocupado.

### 12.4 Anti-snowball (resumo)

- Custo de expansão +8% por província acima de 5 (4.1).
- Bônus de regeneração para nações pequenas (8.5).
- Bots priorizam o líder acima de 35% do Score (9.4).
- Respawn único para eliminados (8.8).

Os pesos definitivos serão ajustados nos testes de balanceamento.

---

## 13. Escopo do MVP

A primeira versão jogável deve conter:

- Mapa com 30 províncias continentais em 3 continentes fixos, com nomes aleatórios por partida.
- Ilhas e arquipélagos neutros e inacessíveis.
- 2 a 4 jogadores humanos, com lobby de 60 s.
- Identidade da nação por **nome do jogador e cor** (sem bandeira e sem nome de país).
- Escolha da capital e recomendações dinâmicas.
- Distribuição de bots após as capitais, com personalidades, tamanhos variados e proteção inicial de 3 minutos.
- Expansão terrestre por adjacência e conquista.
- Geografia híbrida e identidade dos continentes.
- Dinheiro e três recursos; Fazenda, Mina, Serraria, Mercado e Quartel.
- Consumo, manutenção e slots de construção.
- Relógio global (1 ano = 60 s) e ciclo econômico de 5 s.
- Reserva compartilhada, ataques contínuos, guarnições e retirada com perda de 10%.
- Declaração de guerra, ocupação, perda de capital e respawn único.
- Regeneração natural e recrutamento econômico.
- Diplomacia mínima: pacto de não agressão, aliança e paz, com reputação.
- Piloto automático para desconexão.
- Bots com máquina de estados e limites contra humanos.
- Tutorial, alertas de ataque e UI mobile-first.
- Score, vitória antecipada e eventos de fase.

### Fora do MVP

- Expansão marítima e colonização de ilhas.
- População detalhada e mercado internacional.
- Árvore tecnológica, Fábrica e Porto.
- Leis e políticas internas.
- Diplomacia avançada e espionagem.
- Bandeiras e nomes de países personalizados.
- Persistência de nações entre partidas.
- IA avançada.
- Geração procedural do formato do mapa.

---

## 14. Arquitetura técnica

Tecnologias: Roblox Studio, Luau, RemoteEvents, ModuleScripts e DataStoreService (persistência futura).

O servidor é **autoritativo** sobre dinheiro, propriedade territorial, construções, tropas, guerras, relógio e decisões dos bots. O cliente cuida da interface, da apresentação do mapa e da interação.

### 14.1 Dados do mapa

`Provinces[id] = { name, neighbors, biome, resources, region, polygon }`

O formato é fixo; nomes, biomas e recursos são sorteados por seed a cada partida.

### 14.2 Validações no servidor

Rate limit por remote, adjacência, posse, custo, limite de ataques, tropas disponíveis e estado de guerra.

### 14.3 Rede

Simulação a **1 Hz** e replicação a \~**2 Hz**, apenas com deltas e apenas para os envolvidos em cada combate.

### 14.4 Nomes

Nomes de jogadores usam o DisplayName da plataforma. Nomes de províncias e de bots vêm de listas internas, então não exigem `TextService:FilterStringAsync`.

### 14.5 Ferramentas de balanceamento

Modo debug com partidas apenas de bots a **10x**, gerando CSV de Score e províncias por minuto, para ajustar os números sem precisar de jogadores reais.

### 14.6 Monetização

Fora do MVP. Reservar um perfil de jogador (estatísticas e cosméticos futuros, como emblemas de cor).

---

## 15. Diretrizes de desenvolvimento

O projeto é desenvolvido progressivamente: primeiro uma versão funcional, depois mais complexidade. A prioridade é validar se expansão, economia, combate e competição entre jogadores são divertidos antes de sistemas avançados.

Os bots devem sempre ter papel complementar. As decisões de balanceamento e de geração do mapa devem preservar oportunidades justas para os humanos, sem eliminar a diversidade geopolítica entre partidas.

---

## 16. Registro de alterações (v1.1 → v1.2)

| Área | Alteração |
| --- | --- |
| Identidade | Removidos bandeira e nome de país. Nação = nome do jogador + cor. Bots com nome gerado. |
| Províncias | Nomes aleatórios, únicos por partida. |
| Tempo | 1 ano = 60 s; ciclo econômico de 5 s (corrige o erro de 45 min ≈ 7,4 anos). |
| Vitória | Fórmula de Score, vitória antecipada, fases e evento global. |
| Anti-snowball | Custo crescente de expansão, bônus de regeneração, rubber-band dos bots. |
| Economia | Consumo, manutenção, slots, upgrades. Mercado e Quartel entram no MVP. |
| Combate | Fórmula base, guarnição, reforço da reserva, casos de borda, declaração de guerra. |
| Diplomacia | Pacto, aliança e paz com reputação, no MVP. |
| Sessão | Lobby, entrada tardia, piloto automático. |
| Bots | Máquina de estados, limites contra humanos, atraso de reação. |
| UX | Mobile-first, tutorial, feedback e alertas de combate. |
| Técnico | Mapa fixo desenhado à mão, validações, rede e modo debug. |
