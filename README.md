# Tolizzo

Guia turístico e de eventos com indicador de lotação em tempo real e cálculo de menor rota entre pontos de interesse. Projeto didático com frontend web estático (GitHub Pages) e backend em Java com Spring Boot.

## Objetivo

Oferecer um sistema digital e acessível para usuários que busquem atividades de turismo e eventos locais em cidades do Brasil, permitindo simular rotas entre Pontos de Interesse em espaços urbanos ou delimitados.

## Escopo do MVP

| No escopo | Fora do escopo |
|---|---|
| Interface web com menu lateral fixo, feed de eventos e mapa interativo lado a lado (Split Screen) | Integração com APIs pagas de GPS/mapas (Google Maps, Mapbox ou similares) |
| Feed e gestão de eventos, com filtro por categoria e vínculo direto entre evento e local | Aplicativo mobile nativo (foco exclusivo na web responsiva nesta versão) |
| Mapeamento e cálculo de rotas via coordenadas bidimensionais (X, Y) e distância euclidiana no backend | Sistema de pagamentos ou venda de ingressos |
| Painel administrativo para cadastro de novos POIs e eventos | Chat ou rede social interna entre usuários |
| Testes e qualidade de código | — |

## Proposta de valor

- **Para gestores públicos e privados:** solução autônoma de turismo e mobilidade em áreas delimitadas, sem custo recorrente de licenças de mapas proprietários e com controle total sobre os dados de POIs e eventos.
- **Para visitantes e cidadãos:** acesso rápido e sem atrito, direto pelo navegador, a um guia de eventos atualizado e rotas simples de navegação, promovendo o comércio local e a cultura regional.

## Funcionalidades

### Visitante
- Login e cadastro de conta.
- Feed de eventos em andamento, com filtro por categoria (Festivais, Museus, Gastronomia) e indicador de lotação.
- Página de detalhe do evento, com cronograma, lotação e localização no mapa.
- Mapa geral com lista de POIs, busca e simulação de menor caminho entre dois pontos.
- Perfil com dados cadastrais, eventos favoritados e histórico de rotas.

### Gestor
- Cadastro de novos pontos de interesse (nome, categoria, coordenadas X e Y).
- Cadastro de novos eventos vinculados a um POI existente.

### Prioridade dos recursos

| Recurso | Descrição | Prioridade |
|---|---|---|
| Mapa interativo | Simulação visual da rota calculada, renderizando eventos e POIs | Alta |
| Simular trajeto | Algoritmo em Java para calcular a distância euclidiana entre coordenadas e indicar o POI mais próximo | Alta |
| Catálogo de eventos | Catálogo visual com filtros por categoria (Festivais, Museus, Gastronomia) | Alta |
| Painel de gestão (CRUD) | Área restrita para cadastro e manutenção de POIs e eventos | Média |
| Autenticação de usuários | Tela de login e cadastro | Média |
| Histórico e favoritos | Salvar eventos de interesse e rotas simuladas recentemente | Média |

## Fluxo do usuário

1. **Entrada / Login** — o usuário acessa a plataforma pelo navegador (GitHub Pages) e realiza o login.
2. **Exploração no feed** — na tela inicial, visualiza o grid de eventos e filtra pela categoria desejada.
3. **Consulta do detalhe** — clica em um card de evento para ver o cronograma e o local associado.
4. **Navegação no mapa** — aciona "Ver no mapa" ou acessa a aba "Mapa geral".
5. **Simulação de rota** — seleciona o POI de origem e destino no painel lateral; o sistema chama a API Java e desenha o menor caminho na tela.

## Arquitetura

```
Frontend (GitHub Pages)  --->  API REST (Spring Boot)  --->  Banco de dados
   HTML/CSS/JS ou SPA          Controller / Service /         H2 (dev) ou
   HashRouter                  Repository / Domain             PostgreSQL (prod)
```

### Backend (Java + Spring Boot)
- **Camadas:** `Controller`, `Service`, `Repository` e `Domain` (entidades `PontoDeInteresse` e `Evento`).
- **Algoritmos:** distância euclidiana entre POIs e menor caminho por grafos (Dijkstra) para o motor de rotas.
- **Validação:** Bean Validation nos dados de entrada e tratamento centralizado de exceções.
- **CORS:** liberado para o domínio do GitHub Pages (`https://usuario.github.io`).
- **Persistência:** H2 em desenvolvimento, PostgreSQL em produção.
- **Documentação da API:** Springdoc OpenAPI / Swagger UI.
- **Testes:** JUnit 5 e Mockito para as regras de negócio, com cobertura medida pelo JaCoCo.

### Frontend (Web)
- Interface estática hospedada no GitHub Pages, usando `HashRouter` para as rotas funcionarem sem configuração de servidor.
- Fallback com JSONs mocados, para o frontend continuar funcionando mesmo sem a API disponível.
- Deploy automatizado via GitHub Actions a cada commit na branch principal.

## Páginas

| Rota | Descrição |
|---|---|
| `/login`, `/cadastro` | Autenticação, em layout split screen |
| `/eventos` | Feed de eventos ativos com indicador de lotação |
| `/evento/:id` | Detalhe do evento, cronograma e ponto no mapa |
| `/mapa` | Mapa geral com lista de POIs e simulação de rota |
| `/conta` | Perfil, favoritos e histórico de rotas |
| `/gestao` | Cadastro de POIs e eventos |

## Tecnologias

**Backend**
- Java 17+
- Spring Boot (Web, Validation, Data JPA)
- H2 / PostgreSQL
- JUnit 5, Mockito, JaCoCo
- Springdoc OpenAPI

**Frontend**
- HTML, CSS e JavaScript (ou framework SPA, conforme a implementação)
- Leaflet.js ou Canvas para o mapa, com plano cartesiano (X, Y) para os POIs
- `localStorage` para sessão do usuário

**Infraestrutura**
- GitHub Pages (frontend)
- GitHub Actions (deploy contínuo do frontend)

## Como rodar localmente

### Backend
```bash
cd backend
./mvnw spring-boot:run
```
A API sobe em `http://localhost:8080`. A documentação Swagger fica em `http://localhost:8080/swagger-ui.html`.

### Frontend
```bash
cd frontend
# abra o index.html diretamente no navegador, ou sirva com um servidor estático:
npx serve .
```
Por padrão, o frontend aponta para `http://localhost:8080`. Ajuste a URL da API em `config.js` (ou equivalente) conforme o ambiente.

### Testes e cobertura
```bash
cd backend
./mvnw test
./mvnw jacoco:report
```
O relatório de cobertura é gerado em `backend/target/site/jacoco/index.html`.

## Estrutura de pastas (sugerida)

```
tolizzo/
├── backend/
│   ├── src/main/java/.../controller/
│   ├── src/main/java/.../service/
│   ├── src/main/java/.../repository/
│   ├── src/main/java/.../domain/
│   └── src/test/java/...
├── frontend/
│   ├── index.html
│   ├── assets/
│   └── mocks/          # JSONs de fallback
└── .github/workflows/
    └── deploy.yml       # deploy do frontend no GitHub Pages
```

## Processo de design

O protótipo de interface foi desenvolvido no Figma com o apoio de ferramentas de inteligência artificial para geração e refinamento do layout (paleta de cores, tipografia, componentes e telas), seguido de ajustes manuais para adequação aos requisitos funcionais do projeto (autenticação, feed de eventos com lotação, mapa geral com simulação de rota, perfil e painel de gestão).

## Modelo de dados (resumo)

**PontoDeInteresse**
- `id`, `nome`, `categoria`, `x`, `y`

**Evento**
- `id`, `titulo`, `categoria`, `pontoDeInteresseId`, `dataInicio`, `dataFim`, `lotacaoAtual`, `capacidadeMaxima`

## Deploy

O frontend é publicado automaticamente no GitHub Pages a cada push na branch `main`, via GitHub Actions. O backend não é hospedado no Pages (que serve apenas conteúdo estático); ele deve ser hospedado separadamente, por exemplo em um serviço de nuvem gratuito voltado a projetos Java, e sua URL configurada no frontend.

## Escopo e propósito

Este é um projeto didático, criado para demonstrar:
- Arquitetura em camadas com Spring Boot.
- Uso de estruturas de grafos e algoritmos de menor caminho em um cenário real.
- Testes automatizados e cobertura de código como prática de qualidade.
- Documentação de API com OpenAPI/Swagger.
- Deploy contínuo de um frontend estático integrado a uma API externa.

## Indicadores de sucesso (KPIs)

| Indicador | Métrica |
|---|---|
| Precisão do algoritmo | 100% de exatidão nos testes unitários das rotas calculadas |
| Qualidade de código | Cobertura de testes ≥ 80% |
| Desempenho da aplicação web | Tempo de carregamento da página no GitHub Pages < 2,0 segundos |

## Cronograma de alto nível

| Marco | Prazo alvo |
|---|---|
| Validação de escopo e protótipo no Figma | Curto prazo |
| Backend base e entidades Java | 30/09 |
| Algoritmo de rotas | 30/09 |
| Frontend web | 30/09 |
| Testes da aplicação | 14/10 |
| Deploy e publicação no GitHub Pages | Curto prazo |

## Riscos e mitigação

| Risco | Mitigação |
|---|---|
| Bloqueio de requisições no GitHub Pages | Configurar explicitamente as origens permitidas na classe `WebMvcConfigurer` do Spring Boot |
| Servidor em nuvem gratuito indisponível | Manter uma alternativa de hospedagem gratuita já mapeada |
| Rota não encontrada (404) ao recarregar a página no GitHub Pages | Usar `HashRouter` na camada de roteamento do frontend |

## Orçamento

| Recurso | Custo estimado |
|---|---|
| Hospedagem do frontend (GitHub Pages) | R$ 0 |
| Hospedagem do backend (serviço de nuvem gratuito) | R$ 0 |
| Banco de dados | R$ 0 |
| Ferramentas de design | R$ 0 |

## Licença

Defina a licença do projeto aqui (por exemplo, MIT), caso pretenda publicá-lo como portfólio.
