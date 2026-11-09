# Risk-Floor Model Routing (roteamento de modelo com piso de risco)

**Um padrão de skill para o Claude que escolhe o modelo *e* o nível de esforço de cada etapa da tarefa, com um esforço mínimo (o "piso de risco") onde o erro custa caro.**

[English](README.md) · Sugestão de melhoria relacionada: [anthropics/claude-code#108611](https://github.com/anthropics/claude-code/issues/108611)

---

## Em resumo

Mandar cada tarefa para o modelo mais barato que dá conta é uma ideia conhecida. A maioria das soluções decide pela **complexidade** ou pelo **custo**.

Este padrão acrescenta uma pergunta antes de cada etapa: **quanto custa um erro aqui?**

Uma etapa que copia um prazo, um valor ou uma citação pode ser simples e mesmo assim não pode errar. Por isso a tabela tem um piso: essas etapas nunca rodam abaixo do esforço médio, qualquer que seja o modelo, e o resultado delas é conferido antes de seguir.

O restante são calibragens pequenas, aprendidas no uso diário em um fluxo de serviços profissionais, que mantêm a escolha barata, previsível e sob controle do usuário.

## O que já é comum

- Tabela que liga tipos de atividade a modelos.
- Subagentes chamados com modelo definido.
- Planejar num modelo e executar em outro.

## O que este padrão acrescenta

| N. | Calibragem | O que faz |
|---|---|---|
| 1 | **Piso de risco no esforço** | Etapas com prazo, valor, citação ou ação irreversível nunca rodam abaixo do médio, e o resultado é conferido antes do uso. No resto, decide o custo. |
| 2 | **Modelo e esforço juntos, por etapa** | Toda sugestão vem em par, `Modelo: X \| Esforço: Y`, etapa por etapa, e não uma vez por conversa. |
| 3 | **O menor esforço que entrega o mesmo resultado** | Raciocínio longo só onde o erro gera retrabalho; conferência mecânica por buscas pontuais, sem reler tudo. |
| 4 | **Refazer o ponto, não a etapa** | Resultado duvidoso de etapa leve: refaz só aquele ponto com um nível acima. |
| 5 | **Divisão clara do trabalho** | Subagentes baratos leem, extraem e buscam. A conversa principal fica com o julgamento, a redação final, o diálogo e toda ação irreversível (enviar, assinar, publicar, apagar). |
| 6 | **Troca com o usuário no circuito, sem ilusão** | A skill define o modelo dos subagentes, mas o da conversa principal só o usuário troca, no seletor, e a troca só vale a partir da mensagem seguinte dele (testado: a troca feita antes de clicar na resposta não valeu naquela mesma rodada). Por isso a pergunta diz com clareza: escolha a opção, troque no seletor e mande uma palavra, como "segue". Marcar a opção sozinha não troca nada. Nessa mensagem, a skill confere o identificador do modelo da sessão e avisa uma vez se a troca não aconteceu. |
| 7 | **Mapa que se corrige** | Correções do usuário, etapas refeitas, modelo mais barato que entrega bem, atividades novas e lançamentos de modelo atualizam a tabela, com data e motivo, para aprovação do usuário. |
| 8 | **Sem cerimônia quando não compensa** | Tarefas de uma ou duas ações dispensam o pré-voo; execuções agendadas informam a sugestão em uma linha e seguem. |

## Como adaptar a outras áreas

O piso de risco é a parte que vale levar. Escolha as categorias em que o erro é fácil de cometer e caro de descobrir:

- **Contabilidade e finanças:** valores, alíquotas, datas de entrega.
- **Software:** migrações de banco, configuração de produção, segredos e exclusões.
- **Pesquisa e publicação:** citações, referências, nomes e números.
- **Operações e compras:** datas de contrato, quantidades, identificação de fornecedores.

## Instalação

Copie [`skill/SKILL.md`](skill/SKILL.md) para uma pasta chamada `model-and-effort-routing` e adicione-a como skill (no Claude Code, em `.claude/skills/` ou `~/.claude/skills/`; no aplicativo Claude, como skill personalizada). Ajuste o mapa (seção 3) e as categorias do piso (seção 4).

## Limitações

- Hoje uma skill não consegue trocar o modelo nem o esforço da conversa principal; ela só sugere e pergunta. A sugestão [#108611](https://github.com/anthropics/claude-code/issues/108611) pede uma forma oficial de aplicar a tabela do próprio usuário automaticamente, com aviso visível e possibilidade de escolha manual.
- O padrão reduz a chance de usar o nível errado; não dispensa conferir o resultado.
- Nomes, preços e limites dos modelos mudam; revise a tabela quando a linha de modelos mudar.

## Licença

[MIT](LICENSE).
