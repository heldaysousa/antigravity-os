# SKILL: git-workflow
Commits: [tipo](escopo): descrição < 72 chars
Tipos: feat | fix | refactor | style | docs | chore | test
Branch: feature/[escopo]-[desc] | fix/[escopo]-[bug]
NUNCA commitar: .env, node_modules, chaves, dados de usuário.
Antes de push: git diff --staged para revisar cada arquivo.
Push em produção: SEMPRE via git-push-safe.sh.
