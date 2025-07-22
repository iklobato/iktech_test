# Desafio Django - Cadastro de Temperos do Sr. Basílio 🧂

**Tempo estimado:** 30 minutos máximo  
**Nível:** Júnior Backend

## Contexto
O Sr. Basílio tem uma loja de temperos e precisa de **um endpoint** para cadastrar novos produtos. Ele tem 3 regras simples mas muito específicas da sua experiência.

## Endpoint Único
```
POST /api/temperos/cadastrar/
```

## Modelo Django Simples

```python
class TemperoBasilio(models.Model):
    codigo_produto: CharField (max 8 chars, único, obrigatório)
    nome: CharField (max 50 chars, obrigatório)
    regiao_origem: CharField (max 30 chars, obrigatório)
    nivel_picancia: IntegerField (1 a 5, obrigatório)
    preco_centavos: IntegerField (obrigatório)
    eh_organico: BooleanField (default=False)
    data_colheita: DateField (obrigatório)
    observacoes: TextField (opcional, max 100 chars)
    criado_em: DateTimeField (auto_now_add=True)
```

## Request Body
```json
{
  "codigo_produto": "TB123456",
  "nome": "Pimenta Malagueta Especial",
  "regiao_origem": "Minas Gerais",
  "nivel_picancia": 4,
  "preco_centavos": 1200,
  "eh_organico": true,
  "data_colheita": "2024-12-01",
  "observacoes": "Colheita da fazenda São João"
}
```

## 3 Regras Simples do Sr. Basílio

### Regra 1: Código do Produto
- Deve começar com "TB" seguido de exatamente 6 números
- Exemplos válidos: "TB123456", "TB999888"
- Exemplos inválidos: "TB12345", "XB123456", "TB1234567"

### Regra 2: Preço de Orgânicos
- Se `eh_organico = true`, então `preco_centavos` deve ser **no mínimo 1000**
- Temperos orgânicos são mais caros!

### Regra 3: Observações para Picantes
- Se `nivel_picancia >= 4`, então `observacoes` é **obrigatório**
- Deve ter pelo menos 10 caracteres explicando a picância

## Response de Sucesso (201 Created)
```json
{
  "status": "TEMPERO_CADASTRADO",
  "codigo_produto": "TB123456", 
  "resumo": {
    "nome": "Pimenta Malagueta Especial",
    "preco_reais": "R$ 12,00",
    "categoria_picancia": "PICANTE",
    "selo_organico": true
  },
  "mensagem": "Tempero cadastrado com sucesso!",
  "criado_em": "2025-01-22T10:30:00Z"
}
```

## Response de Erro (422)
```json
{
  "status": "ERRO_VALIDACAO",
  "erros": {
    "codigo_produto": ["Deve começar com TB seguido de 6 números"],
    "preco_centavos": ["Temperos orgânicos custam no mínimo R$ 10,00"],
    "observacoes": ["Obrigatório para temperos picantes (nível 4 ou 5)"]
  }
}
```

## Campos Calculados na Response

Adicione estes campos calculados:

1. **`preco_reais`**: Converter centavos para formato "R$ X,XX"
2. **`categoria_picancia`**: 
   - 1-2: "SUAVE"
   - 3: "MEDIO"
   - 4-5: "PICANTE"
3. **`selo_organico`**: True se eh_organico = true

## Validações Adicionais Simples

- `data_colheita` não pode ser no futuro
- `nivel_picancia` deve estar entre 1 e 5
- `preco_centavos` deve ser positivo

## Headers na Response
```
X-Loja-Basilio: "Temperos-Artesanais"
Content-Type: "application/json"
```

## Exemplo de Teste
```bash
# Teste sucesso
curl -X POST http://localhost:8000/api/temperos/cadastrar/ \
  -H "Content-Type: application/json" \
  -d '{"codigo_produto":"TB123456","nome":"Açafrão","regiao_origem":"Bahia","nivel_picancia":2,"preco_centavos":800,"eh_organico":false,"data_colheita":"2024-11-15"}'

# Teste erro
curl -X POST http://localhost:8000/api/temperos/cadastrar/ \
  -H "Content-Type: application/json" \
  -d '{"codigo_produto":"XX123456","nome":"Pimenta","regiao_origem":"São Paulo","nivel_picancia":5,"preco_centavos":500,"eh_organico":true,"data_colheita":"2025-12-01"}'
```

## O que Entregar
1. `models.py` - Modelo TemperoBasilio
2. `views.py` - View do endpoint (pode usar DRF ou Django puro)
3. `urls.py` - Configuração da rota
4. Exemplo de teste funcionando

## Critérios de Avaliação
- ✅ Modelo correto (30%)
- ✅ 3 regras do Sr. Basílio funcionando (40%)
- ✅ Response no formato correto (20%)
- ✅ Campos calculados (10%)

## Dicas
- Use `forms.ModelForm` ou DRF Serializer
- Para validação customizada: método `clean()` ou `validate()`
- Para converter centavos: `f"R$ {valor/100:.2f}"`
- Para validar regex: `import re` e `re.match()`

**Foco nas 3 regras específicas do Sr. Basílio! Elas são o coração do desafio! 🧂**
