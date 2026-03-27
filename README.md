# Calculadora de ROI · MIIA

Ferramenta de cálculo de retorno sobre investimento para instituições de ensino que avaliam a adoção das soluções de IA da [MIIA](https://miia.tech). O lead preenche os dados da sua instituição e recebe em tempo real a estimativa de horas recuperadas e economia financeira anual.

**Deploy:** [gustavo130803.github.io/calculadora-miia](https://gustavo130803.github.io/calculadora-miia/)

---

## Fluxo do usuário

```mermaid
flowchart TD
    A([Acessa a calculadora]) --> B[Informa valor da hora/aula do docente]
    B --> C{Seleciona os módulos ativos}

    C --> D[Correção de Discursivas]
    C --> E[Geração de Questões Inéditas]
    C --> F[Diagramação Automática de Provas]

    D --> D1[Volume de provas + frequência]
    E --> E1[Volume de questões + frequência]
    F --> F1[Volume de provas + frequência]

    D1 & E1 & F1 --> G[Cálculo em tempo real]

    G --> H1[Economia Financeira / Ano]
    G --> H2[Horas Recuperadas / Ano]
    G --> H3[Eficiência Ganha +22%]

    H1 & H2 & H3 --> I{Ação do lead}

    I --> J1[Falar com especialista — WhatsApp]
    I --> J2[Falar com especialista — E-mail]
    I --> J3[Compartilhar resultado via WhatsApp]
    I --> J4[Compartilhar resultado via E-mail]
    I --> J5[Copiar link do resultado]
    I --> J6[Exportar como PDF]
```

---

## Lógica de cálculo

```mermaid
flowchart LR
    subgraph INPUTS
        V[Volume de unidades]
        F[Frequência selecionada]
        VH[Valor hora docente R$]
    end

    subgraph CONVERSÃO
        V --> AN[Total anual de unidades]
        F -->|dia ×200 / semana ×40 / mês ×12 / semestre ×2 / ano ×1| AN
    end

    subgraph POR_MÓDULO
        AN --> |"× 16 min ÷ 60"| HC[Horas salvas — Correção]
        AN --> |"× 27 min ÷ 60"| HG[Horas salvas — Geração]
        AN --> |"× 43 min ÷ 60"| HD[Horas salvas — Diagramação]
    end

    subgraph OUTPUTS
        HC & HG & HD --> TH[Total de horas/ano]
        TH --> |"× R$ hora"| TE[Economia financeira/ano]
        VH --> TE
    end
```

---

## Arquitetura

```mermaid
graph TD
    subgraph Repositório
        HTML[index.html — app completo]
        WF[.github/workflows/static.yml]
    end

    subgraph GitHub_Actions
        WF -->|push na main| CI[Deploy automático]
        CI --> PAGES[GitHub Pages]
    end

    subgraph Browser
        PAGES --> DOM[HTML + Tailwind CDN]
        DOM --> JS[JavaScript puro — calc]
        JS -->|oninput / onchange| JS
        JS -->|buildShareURL| URL[URL com parâmetros]
        URL -->|loadFromURL| JS
    end
```

---

## Módulos disponíveis

```mermaid
classDiagram
    class Modulo {
        +string nome
        +int minEconomizados
        +bool ativo
        +toggleMod()
    }

    class CorrecaoDiscursivas {
        +int volumeProvas
        +string frequencia
        +calcHoras() volume × freq × 16÷60
    }

    class GeracaoQuestoes {
        +int volumeQuestoes
        +string frequencia
        +calcHoras() volume × freq × 27÷60
    }

    class DiagramacaoProvas {
        +int volumeProvas
        +string frequencia
        +calcHoras() volume × freq × 43÷60
    }

    Modulo <|-- CorrecaoDiscursivas
    Modulo <|-- GeracaoQuestoes
    Modulo <|-- DiagramacaoProvas
```

---

## Ações disponíveis após o cálculo

```mermaid
flowchart LR
    R[Resultado calculado] --> A1[Falar com especialista\nWhatsApp]
    R --> A2[Falar com especialista\nE-mail]
    R --> A3[Compartilhar resultado\nWhatsApp — link com parâmetros]
    R --> A4[Compartilhar resultado\nE-mail — link com parâmetros]
    R --> A5[Copiar link do resultado]
    R --> A6[Exportar PDF\n1 página · cores MIIA]

    A3 & A4 & A5 --> L[Link compartilhável\nURL com query params\nAuto-preenche ao abrir]
```

---

## Stack

| Camada | Tecnologia |
|---|---|
| Frontend | HTML5 + CSS3 + JavaScript vanilla |
| Estilo | Tailwind CSS via CDN |
| Fontes | Google Fonts — Montserrat (números/títulos) + Sora (corpo) |
| Hospedagem | GitHub Pages (gratuito) |
| CI/CD | GitHub Actions — deploy automático no push |

---

## Como rodar localmente

```bash
# Sem build necessário — abra direto no browser
open index.html
```

Ou use o Live Server do VS Code para hot reload.
