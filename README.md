# Uma comparação entre modelos balanceados e desbalanceados (Bank Marketing)

Este repositório contém um estudo aplicado ao dataset **Bank Marketing** (Kaggle/UCI) para comparar o comportamento de modelos de classificação em **dois cenários**:

1) **Dados desbalanceados** (realidade bruta do problema)  
2) **Dados balanceados via undersampling** (igualando a classe majoritária à minoritária)

O foco é entender o impacto do desbalanceamento em métricas críticas para negócio (especialmente **Recall**), já que em campanhas de marketing/CRM **falsos negativos** representam oportunidade perdida de conversão.

---

## 🎯 Problema

Prever a variável-alvo **`y`**:
- `yes`: cliente aderiu ao depósito a prazo (evento raro)
- `no`: cliente não aderiu (maioria)

Em bases altamente desbalanceadas, muitos modelos tendem a “aprender o caminho fácil” e prever sempre a classe majoritária, o que gera:
- **Recall baixo**
- Muitos **falsos negativos**
- Campanhas menos eficientes (mais contatos desperdiçados / menos conversão)

---

## 📌 Dataset

- **Nome:** Bank Marketing (bank-additional-full.csv)
- **Tamanho:** ~41.188 linhas, 20 atributos (2008–2010)
- **Origem:** Kaggle (dataset popular de campanhas telefônicas)

### Observações importantes do dataset (tratamentos aplicados)
- `duration` é altamente correlacionada com `y`, mas **não estaria disponível antes da ligação** → **removida** para evitar leakage.
- `pdays = 999` indica “nunca contatado” → convertido para `NaN`; como gerou grande volume de ausências, a feature foi **excluída**.
- Registros com categorias `unknown` em features categóricas foram **removidos**.
- Features categóricas de **baixa cardinalidade (< 30)** foram transformadas via **OneHotEncoder**.
- Variáveis numéricas foram padronizadas com **StandardScaler**.

---

## 🧱 Pipeline (visão geral)

1. **Train/Test Split** (antes de qualquer tratamento para reduzir risco de leakage)
2. Limpeza e regras de negócio:
   - remover `duration`
   - tratar `pdays`
   - remover linhas com `unknown`
3. **Encoding**:
   - OneHotEncoder para categóricas (baixa cardinalidade)
4. **Scaling**:
   - StandardScaler para numéricas
5. **Feature Selection**:
   - **Random Forest Feature Importance** com cutoff de **20%**
6. Modelagem em dois cenários:
   - **Desbalanceado**
   - **Balanceado (undersampling)**

---

## 🤖 Modelos avaliados

- **Decision Tree**
- **Random Forest**
- **XGBoost**

### Métricas utilizadas
- ROC AUC
- Gini
- KS
- Precision
- Recall
- F1-score
- Confusion Matrix
- Curvas ROC e Precision–Recall
- Ordenação por score (ranking/decis)

---

## 🧪 Resultados (highlights)

### 🔻 Cenário 1 — Dados desbalanceados
Apesar de métricas globais “boas” (AUC ~0.79–0.80), os modelos apresentaram **Recall extremamente baixo (~17%)**, com muitos falsos negativos:

- **Decision Tree**: Recall ~0.17  
- **Random Forest**: Recall ~0.16  
- **XGBoost**: Recall ~0.17  

**Interpretação prática:** o modelo parece bom pela AUC, mas falha em identificar quem realmente tem chance de aderir — ruim para priorização de leads.

---

### 🔺 Cenário 2 — Dados balanceados (undersampling)
O balanceamento alterou o comportamento estrutural dos modelos e aumentou muito a capacidade de detectar a classe positiva:

- **Decision Tree**: Recall **0.676**
- **Random Forest**: Recall **0.684**
- **XGBoost**: Recall **0.705** (com Precision **0.815** e F1 **0.756**)

**Destaque:** o **XGBoost balanceado** foi o melhor equilíbrio entre Recall, Precision, F1 e estabilidade (métricas e curvas coerentes entre treino e teste).

> Principal lição: não basta “escolher o melhor modelo”.  
> Em eventos raros, **dar condições para o modelo aprender** (balanceamento/weights/SMOTE) é uma decisão estratégica.

---

## 📁 Arquivos do projeto

- `Propensão de Compras de Produtos Bancários.ipynb`  
  Notebook principal com:
  - preparação dos dados
  - feature selection
  - treino e avaliação (desbalanceado vs balanceado)
  - visualizações e métricas

> Observação: o notebook atualmente referencia caminhos do Google Drive (Colab). Veja abaixo como adaptar.

---
