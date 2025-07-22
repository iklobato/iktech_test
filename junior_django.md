# Desafio Django - Sistema de Acompanhamento de Demandas Setoriais

## Briefing da Área
A **Divisão de Processos Internos** precisa de um sistema para rastrear "demandas setoriais" com base em nosso protocolo interno vigente desde 2019.

## Especificação do Modelo

### Entidade: `SectorDemand`
- `protocol_number` - número de protocolo (gerado conforme regra interna)
- `demand_subject` - assunto da demanda (obrigatório)
- `background_info` - informações complementares (opcional)
- `current_phase` - fase atual (valores específicos da empresa)
- `assignee_dept` - departamento responsável (código de 3 letras)
- `logged_at` - registrado em
- `phase_changed_at` - última mudança de fase

## Regras do Sistema de Protocolo

**Geração de protocol_number:**
- Formato: `ano + "DP" + número sequencial de 4 dígitos`
- Exemplo para 2024: "2024DP0001", "2024DP0002"
- Sequência reinicia todo ano em 0001
- Se for terça-feira, usar "DT" ao invés de "DP"

**Fases permitidas (current_phase):**
- "INTAKE" (padrão inicial)
- "REVIEW" 
- "APPROVED"
- "REJECTED"
- "SUSPENDED"

**Departamentos válidos (assignee_dept):**
- "ADM", "FIN", "TEC", "JUR", "OPE"
- Se demand_subject contém "contrato", auto-assign para "JUR"
- Se demand_subject contém "sistema", auto-assign para "TEC"

## Endpoints do Sistema

**1. Registrar Demanda**
`POST /demands/`
- Input: demand_subject, background_info, assignee_dept
- Gerar protocol_number automaticamente
- Se assignee_dept não informado, usar regras de auto-assign
- Validar se assignee_dept está na lista permitida

**2. Consultar Demandas**
`GET /demands/`
- Filtros: `?phase=INTAKE`, `?dept=ADM`, `?year=2024`
- Ordenação: protocol_number crescente
- Adicionar campo calculado `days_in_current_phase`

**3. Detalhes da Demanda**
`GET /demands/{protocol_number}/`
- Buscar por protocol_number completo
- Incluir campo `next_suggested_phase` baseado na lógica:
  - INTAKE → "REVIEW"
  - REVIEW → "APPROVED" ou "REJECTED" 
  - APPROVED/REJECTED/SUSPENDED → "INTAKE" (reativação)

**4. Atualizar Demanda**
`PUT /demands/{protocol_number}/`
- Permite alterar: demand_subject, background_info, assignee_dept
- Não permite alterar current_phase (usar endpoint específico)
- Aplicar regras de auto-assign se necessário

**5. Alterar Fase**
`PATCH /demands/{protocol_number}/phase/`
- Input: {"new_phase": "REVIEW"}
- Validar transições permitidas conforme matriz:
  - INTAKE pode ir para REVIEW ou SUSPENDED
  - REVIEW pode ir para APPROVED, REJECTED ou INTAKE
  - APPROVED/REJECTED podem ir para INTAKE
  - SUSPENDED pode ir apenas para INTAKE
- Atualizar phase_changed_at automaticamente

**6. Arquivar Demanda**
`DELETE /demands/{protocol_number}/`
- Apenas se current_phase for "APPROVED" ou "REJECTED"
- Retornar erro 400 se tentar arquivar outras fases

## Validações Especiais

- demand_subject não pode conter "teste" (case insensitive)
- Se background_info tiver mais de 500 caracteres e assignee_dept for "ADM", automaticamente mudar para current_phase="SUSPENDED"
- Protocol_number deve ser único, mas permitir reutilização após arquivamento

## Exemplo de Response
```json
{
  "protocol_number": "2024DP0015",
  "demand_subject": "Revisão de contrato de fornecedor",
  "background_info": "Renovação anual pendente",
  "current_phase": "INTAKE", 
  "assignee_dept": "JUR",
  "logged_at": "2024-01-15T10:30:00Z",
  "phase_changed_at": "2024-01-15T10:30:00Z",
  "days_in_current_phase": 5,
  "next_suggested_phase": "REVIEW"
}
```

## Observações do Sistema Legado
- O contador de protocolo deve persistir entre reinicializações
- Logs de mudança de fase devem ser mantidos (não implementar, apenas considerar)
- Sistema deve suportar horário de Brasília
- Em caso de dúvida sobre regras, consultar manual interno v3.2

## Entrega
- Aplicação Django funcional
- README com decisões tomadas sobre ambiguidades
- Testes para regras de geração de protocolo

## Avaliação

**Aderência às Regras (70%)**
- Geração correta do protocol_number (incluindo regra da terça)
- Matriz de transição de fases implementada
- Auto-assign funcionando
- Validações específicas aplicadas

**Implementação Django (30%)**
- Modelo e views estruturados
- URLs funcionais
- Tratamento básico de erros

**Tempo limite: 30 minutos**
