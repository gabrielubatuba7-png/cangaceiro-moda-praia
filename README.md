# Cangaceiro Moda Praia — plataforma full stack inicial

## O que funciona no código
- Worker Cloudflare com API e arquivos estáticos
- D1: produtos, candidaturas, contas, sessões, parceiros, cupons, pedidos e comissões
- Autenticação por senha PBKDF2, tokens aleatórios com hash no banco, verificação de papéis
- Loja com carrinho, registro de pedidos pendentes e aplicação de cupom calculada no servidor
- Cadastro de candidatos, cadastro administrativo de parceiros e cupons
- Painel de vendas atribuídas, painel administrativo, upload e reprodução de vídeos via R2

## Limitações importantes — NÃO está pronto para operação comercial
- Gateway Pix/cartão, confirmação automática por webhook e frete por CEP **não integrados**.
- Pedidos ficam pendentes até confirmação manual pelo administrador; esta marcação NÃO comprova pagamento.
- Faltam fluxo de aprovação de candidatura, recuperação/verificação de email, convites seguros, gestão completa de estoque, reembolso e repasses, privacidade/LGPD, testes automatizados, observabilidade e mecanismos antifraude.
- Vídeos e fotografias originais não estão no projeto: devem ser fornecidos e enviados.
- Fotos de produtos ainda não têm upload/renderização; arte abstrata é sinalizada como ilustração de interface.
- O checkout é um registro de pedido, não cobrança. Não anunciar como loja pronta para receber pagamentos.
- O exemplo administrativo cria senha temporária manualmente; substituir por convite de ativação seguro.
- O código ainda requer testes de integração e revisão de segurança antes de publicar em produção.

## Implantação em ambiente de testes
1. Instale Node.js 20+ e execute `npm install`.
2. `npx wrangler login` e `npx wrangler d1 create cangaceiro-db`.
3. Substitua `SUBSTITUA_PELO_ID_DO_SEU_D1` no `wrangler.jsonc` pelo ID retornado.
4. `npx wrangler r2 bucket create cangaceiro-media` (habilite R2 na conta se necessário).
5. `npx wrangler secret put ADMIN_SETUP_KEY` (chave aleatória longa; nunca publicar).
6. `npm run db:remote` e `npm run deploy`.
7. Crie o primeiro administrador com requisição POST HTTPS `/api/setup`, header `x-setup-key` igual ao segredo e JSON `{"name":"Administrador","email":"SEU_EMAIL","password":"SENHA_FORTE_COM_12_OU_MAIS"}`. Esta rota só permite o primeiro administrador. Não compartilhe a chave.
8. Faça login em `/` > Minha conta e entre no painel Admin. Cadastre produtos, parceiros e cupons.
9. Antes de liberar para clientes, configurar pagamentos, frete, e-mail, revisão de segurança e testes end-to-end.

### Local
Após criar D1 e R2, execute `npm run db:local` e `npm run dev`. A chave ADMIN_SETUP_KEY pode ser definida no arquivo local `.dev.vars` (nunca commitar).

### Segurança
Não hospede este repositório com segredos, não compartilhe senhas de usuários e não publique o primeiro administrador usando senhas padrão. O endpoint de pedidos exige reforço contra abuso e limites por IP antes de uso público. Não substitua o Worker atual sem validar a nova aplicação em endereço separado.
