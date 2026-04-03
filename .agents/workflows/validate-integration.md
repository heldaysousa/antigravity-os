---
description: Validação de integração cross-layer. Garante que frontend, backend, database e deploy estão alinhados. Baseado no composer INNFLOW.
---

# Validação de Integração

## Quando Usar

- Após múltiplas tasks de diferentes domínios serem completadas
- Antes de deploy para garantir que tudo funciona junto
- Quando há mudanças em APIs, schemas ou contratos

## Checklist de Integração

### API Contract Validation

- [ ] Frontend API calls correspondem aos endpoints backend
- [ ] Request/response types são consistentes
- [ ] Error handling está alinhado entre camadas
- [ ] Authentication tokens fluem corretamente

### Data Flow Verification

- [ ] Database schema corresponde aos models backend
- [ ] Frontend state corresponde ao shape das API responses
- [ ] Type definitions são compartilhadas ou consistentes
- [ ] Null/undefined handling é consistente

### Configuration Alignment

- [ ] Environment variables documentadas
- [ ] Ports e URLs consistentes
- [ ] CORS configurado para origem do frontend
- [ ] Auth configuration alinhado entre serviços

### Cross-Component Compatibility

- [ ] Import paths corretos
- [ ] Shared types/interfaces existem onde necessário
- [ ] Dependencies sem conflito
- [ ] Build configurations compatíveis

## Procedimento

1. **Scan mudanças recentes** — Identificar arquivos modificados
2. **Mapear integration points** — Onde componentes se conectam
3. **Validar contratos** — Verificar que interfaces batem
4. **Testar assumptions** — Verificar fluxo de dados end-to-end
5. **Corrigir issues** — Fazer correções diretamente
6. **Documentar gaps** — Notar trabalho de integração restante

## Output Format

```markdown
## Componentes Revisados

- [Lista componentes analisados]

## Integration Points

- [API endpoint] ↔ [Frontend call]
- [DB model] ↔ [Backend service]

## Issues Encontrados e Corrigidos

1. **[Issue]**: [Descrição]
   - Arquivo: [path]
   - Fix: [O que mudou]

## Issues Pendentes

1. **[Issue]**: [Descrição]
   - Recomendação: [O que fazer]

## Passos de Verificação

1. [Como verificar que a integração funciona]
```

## Padrões Comuns de Fix

### Frontend ↔ Backend

```typescript
// Alinhar shapes de dados
// Frontend espera { name } mas backend retorna { userName }
// Fix: alinhar ao contrato do backend
```

### Environment Variables

```bash
# Garantir que ambos serviços usam mesma config
# Backend: API_PORT=3001
# Frontend: VITE_API_URL=http://localhost:3001
```
