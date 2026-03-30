# Calculadora de ROI · MIIA

Ferramenta de cálculo de retorno sobre investimento para instituições de ensino que avaliam a adoção das soluções de IA da [MIIA](https://miia.tech). O lead preenche os dados da sua instituição e recebe em tempo real o **ROI líquido** — economia com docentes menos o custo dos serviços MIIA.

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
    E --> E1[Tipo Objetiva ou Discursiva\nVolume de questões + frequência]
    F --> F1[Volume de provas + frequência]

    D1 & E1 & F1 --> G[Cálculo em tempo real]

    G --> H1[ROI Líquido / Ano\neconomia docentes − custo MIIA]
    G --> H2[Horas Recuperadas / Ano]
    G --> H3[Eficiência Ganha +22%]

    H1 & H2 & H3 --> I{Ação do lead}

    I --> J1[Falar com especialista — WhatsApp]
    I --> J2[Falar com especialista — E-mail]
    I --> J3[Compartilhar resultado via WhatsApp]
    I --> J4[Compartilhar resultado via E-mail]
    I --> J5[Copiar link do resultado]
    I --> J6[Exportar PDF — 1 página · cores MIIA]
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
        V --> AN[Volume anual]
        F -->|dia ×200 / semana ×40 / mês ×12 / semestre ×2 / ano ×1| AN
        AN --> |÷12| MN[Volume mensal\npara lookup de tier]
    end

    subgraph ECONOMIA_DOCENTES
        AN --> |"× 16 min ÷ 60"| HC[Horas — Correção]
        AN --> |"× 27 min ÷ 60"| HG[Horas — Geração]
        AN --> |"× 43 min ÷ 60"| HD[Horas — Diagramação]
        HC & HG & HD --> TH[Total horas/ano]
        TH --> |"× R$/hora"| EB[Economia Bruta]
        VH --> EB
    end

    subgraph CUSTO_MIIA
        MN --> |lookup tier| PC[Preço por unidade]
        AN --> CM[Custo MIIA anual\n= vol. anual × preço]
        PC --> CM
    end

    subgraph ROI
        EB --> |"−"| RL[ROI Líquido]
        CM --> |"−"| RL
    end
```

---

## Tabelas de preço MIIA

### Correção de Discursivas — R$/prova (tier por volume mensal)

| Volume mensal | R$/prova |
|---|---|
| até 15.000 | R$ 1,60 |
| 15.001 – 30.000 | R$ 1,30 |
| 30.001 – 60.000 | R$ 1,00 |
| 60.001 – 90.000 | R$ 0,80 |
| 90.001 – 300.000 | R$ 0,65 |
| 300.001 – 600.000 | R$ 0,60 |
| 600.001 – 1.000.000 | R$ 0,50 |
| acima de 1.000.000 | R$ 0,45 |

### Geração de Questões — R$/questão (tier por volume mensal)

| Volume mensal | Objetiva | Discursiva |
|---|---|---|
| até 1.000 | R$ 10,00 | R$ 20,00 |
| 1.001 – 2.000 | R$ 8,50 | R$ 17,00 |
| 2.001 – 3.000 | R$ 7,00 | R$ 14,00 |
| 3.001 – 5.000 | R$ 5,50 | R$ 11,00 |
| 5.001 – 7.000 | R$ 4,00 | R$ 8,00 |
| 7.001 – 10.000 | R$ 3,00 | R$ 6,00 |
| acima de 10.000 | R$ 2,50 | R$ 5,00 |

> **Diagramação:** preço ainda não cadastrado — custo MIIA não deduzido neste módulo.

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
        DOM --> JS[JavaScript puro]
        JS -->|oninput / onchange| JS
        JS -->|precoCorrecao / precoGeracao| TIER[Lookup de tier]
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
        +float precoMIIA
        +toggleMod()
        +calcROI() economiaBruta - custoMIIA
    }

    class CorrecaoDiscursivas {
        +int volumeProvas
        +string frequencia
        +calcHoras() volume × freq × 16÷60
        +precoCorrecao(volMes) lookup tier
    }

    class GeracaoQuestoes {
        +int volumeQuestoes
        +string frequencia
        +string tipo obj ou disc
        +calcHoras() volume × freq × 27÷60
        +precoGeracao(volMes, tipo) lookup tier
    }

    class DiagramacaoProvas {
        +int volumeProvas
        +string frequencia
        +calcHoras() volume × freq × 43÷60
        +precoMIIA pendente
    }

    Modulo <|-- CorrecaoDiscursivas
    Modulo <|-- GeracaoQuestoes
    Modulo <|-- DiagramacaoProvas
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
