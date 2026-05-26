# Mini-Projeto 1 — SBC de Triagem Médica Simples

**Disciplina:** Sistemas Baseados em Conhecimento

---

## Domínio: Triagem Médica de Urgência

Este projeto implementa um **Sistema Baseado em Conhecimento (SBC)** de regras IF–THEN para triagem médica de urgência usando a biblioteca **Experta** (Python, baseada em CLIPS). O sistema avalia sinais vitais e sintomas de um paciente e recomenda o nível de prioridade de atendimento (Emergência, Urgência Alta, Urgência Média ou Urgência Baixa).

---

## Regras em Linguagem Natural

O sistema contém **10 regras** organizadas em **3 níveis de encadeamento** (forward chaining):

### Nível 1 — Inferência de condições primárias (fatos derivados)

| # | Regra | Descrição |
|---|-------|-----------|
| R1 | SE temperatura > 39,0 °C → **febre_alta** | Classifica febre alta |
| R2 | SE temperatura entre 37,8 e 39,0 °C → **febre_leve** | Classifica febre leve |
| R3 | SE saturação_o2 < 92 % → **hipoxia_grave** | Detecta hipóxia grave |
| R4 | SE pressao_sistolica < 90 mmHg → **hipotensao** | Detecta hipotensão arterial |

### Nível 2 — Combinação de condições (síndromes derivadas)

| # | Regra | Descrição |
|---|-------|-----------|
| R5 | SE febre_alta E sintoma(dispneia) → **sindrome_respiratoria** | Síndrome respiratória aguda |
| R6 | SE febre_leve E sintoma(tosse_seca) → **suspeita_virose** | Suspeita de virose respiratória |
| R7 | SE hipoxia_grave E sintoma(dor_toracica) → **risco_cardiaco** | Risco cardíaco elevado |

### Nível 3 — Recomendação de prioridade (decisão final)

| # | Regra | Descrição |
|---|-------|-----------|
| R8 | SE hipotensao OU risco_cardiaco → **EMERGÊNCIA** (salience 100) | Prioridade máxima — risco de vida imediato |
| R9 | SE sindrome_respiratoria → **URGÊNCIA ALTA** (salience 80) | Atendimento em até 30 min |
| R10 | SE suspeita_virose → **URGÊNCIA MÉDIA** (salience 60) | Atendimento em até 2 h |

> **Resolução de conflito:** a estratégia `salience` garante que regras de maior gravidade disparem antes. A keyword `NOT` é usada em R10 para evitar duplicidade com pacientes que já receberam classificação superior.

---

## 3 Casos de Teste

### Caso 1 — Emergência (resultado esperado: EMERGÊNCIA)
```
paciente: "Carlos"
temperatura: 40.2 °C  →  febre_alta (R1)
saturacao_o2: 88 %    →  hipoxia_grave (R3)
sintomas: ["dor_toracica", "dispneia"]
                       →  risco_cardiaco (R7) + sindrome_respiratoria (R5)
                       →  EMERGÊNCIA (R8, salience 100)
```

### Caso 2 — Urgência Alta (resultado esperado: URGÊNCIA ALTA)
```
paciente: "Ana"
temperatura: 39.5 °C  →  febre_alta (R1)
saturacao_o2: 96 %
sintomas: ["dispneia"]
                       →  sindrome_respiratoria (R5)
                       →  URGÊNCIA ALTA (R9, salience 80)
```

### Caso 3 — Urgência Média (resultado esperado: URGÊNCIA MÉDIA)
```
paciente: "Beatriz"
temperatura: 38.3 °C  →  febre_leve (R2)
saturacao_o2: 97 %
pressao_sistolica: 120 mmHg
sintomas: ["tosse_seca"]
                       →  suspeita_virose (R6)
                       →  URGÊNCIA MÉDIA (R10, salience 60)
```

---

## Como Executar

### Google Colab (recomendado)
Abra o notebook `triagem_medica_sbc.ipynb` no Google Colab e execute todas as células em ordem (Runtime → Run all).

### Local
```bash
pip install experta
jupyter notebook triagem_medica_sbc.ipynb
```

---

## Equipe

*João Vitor Cardoso Beltrão*
