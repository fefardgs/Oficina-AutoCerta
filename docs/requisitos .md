# 1. Requisitos Funcionais e Regras de Negócio

## Requisitos Funcionais (RF)
* **RF01 - Autenticação e Gestão de Sessão**: Permitir login de usuários cadastrados fornecendo login e senha. A sessão ativa é mantida via cookie HTTP assinado via express-session com expiração de 30 minutos.
* **RF02 - Encerrar Sessão (Logout)**: Permitir o encerramento da sessão ativa a qualquer momento chamando POST /api/logout, destruindo o cookie no servidor.
* **RF03 - Checagem de Sessão do Operador**: Fornecer a rota GET /api/me para validar a sessão e retornar o nome e ID do usuário autenticado.
* **RF04 - Gestão de Clientes (CRUD)**: Permitir o cadastro, edição e listagem de clientes com nome, cpf, telefone e email.
* **RF05 - Busca/Filtro de Clientes**: Permitir a busca dinâmica de clientes por nome, e-mail, telefone ou pelos dígitos numéricos do CPF.
* **RF06 - Gestão de Veículos**: Permitir cadastrar e listar veículos informando placa, modelo, tipo, marca e associando-os a um cliente_id existente.
* **RF07 - Gestão de Agendamentos**: Permitir cadastrar e listar agendamentos vinculando um veículo (veiculo_id), a data_agendamento e a descrição do servico.
* **RF08 - Reagendamento de Serviços**: Permitir alterar exclusivamente a data_agendamento de um agendamento pré-existente via PUT /api/agendamentos/:id.

## Regras de Negócio (RN)
* **RN01 - Restrição de Acesso às APIs**: Todas as rotas em /api/* (exceto /api/login e /api/logout) exigem sessão autenticada gerenciada pelo middleware exigirLogin. Requisições sem sessão válida retornam HTTP 401 Unauthorized.
* **RN02 - Criptografia do CPF (AES-256-GCM)**: O CPF do cliente não pode ser gravado em texto puro. Ele deve ser armazenado no banco cifrado com AES-256-GCM e chave derivada via scryptSync (seguranca.js).
* **RN03 - Armazenamento de Senha em Hash (Bcrypt)**: As senhas dos usuários nunca são gravadas em texto puro, devendo ser validadas e mantidas como hash Bcrypt (fator de custo 8).
* **RN04 - Validação e Unicidade do CPF**:
  * O CPF deve ter exatamente 11 dígitos numéricos após a remoção de caracteres não numéricos.
  * Não é permitido cadastrar ou atualizar dois clientes com o mesmo CPF no banco de dados.
* **RN05 - Validação e Unicidade de Placa**:
  * A placa é tratada sem traços/espaços e convertida para maiúsculas.
  * Deve atender à Regex /^[A-Z]{3}\d[A-Z0-9]\d{2}$/, cobrindo o padrão tradicional (ABC1234) e Mercosul (ABC1D23).
  * Não se pode cadastrar duas vezes a mesma placa (placa UNIQUE).
* **RN06 - Rigor na Validação de Datas**: A data de agendamento deve estar no formato YYYY-MM-DD e ser uma data real válida no calendário (impedindo valores inválidos como 2026-02-31).
* **RN07 - Integridade Referencial**: Veículos exigem um cliente_id válido e cadastrado; agendamentos exigem um veiculo_id válido e cadastrado.