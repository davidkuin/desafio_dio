import datetime

# Lista de usuários e contas
usuarios = []
contas = []

# Função para criar um novo usuário
def criar_usuario():
    cpf = input("Digite o CPF (somente números): ").strip()
    if any(usuario["cpf"] == cpf for usuario in usuarios):
        print("❌ CPF já cadastrado.")
        return

    nome = input("Nome completo: ").strip()
    nascimento = input("Data de nascimento (dd/mm/aaaa): ").strip()
    endereco = input("Endereço (Rua, Nº - Bairro - Cidade/UF): ").strip()

    usuario = {
        "nome": nome,
        "nascimento": nascimento,
        "cpf": cpf,
        "endereco": endereco
    }

    usuarios.append(usuario)
    print("✅ Usuário criado com sucesso!")

# Função auxiliar para buscar um usuário pelo CPF
def encontrar_usuario(cpf):
    for usuario in usuarios:
        if usuario["cpf"] == cpf:
            return usuario
    return None

# Função para criar conta corrente vinculada ao CPF
def criar_conta():
    cpf = input("Informe o CPF do titular: ").strip()
    usuario = encontrar_usuario(cpf)

    if not usuario:
        print("❌ Usuário não encontrado. Crie o usuário antes de abrir uma conta.")
        return

    numero = len(contas) + 1
    conta = {
        "agencia": "0001",
        "numero": numero,
        "usuario": usuario,
        "saldo": 0.0,
        "extrato": [],
        "saques_realizados": 0
    }

    contas.append(conta)
    print(f"✅ Conta {numero} criada com sucesso para {usuario['nome']}.")

# Função para realizar depósito
def depositar(conta):
    valor = float(input("Informe o valor do depósito: R$ ").strip())
    if valor <= 0:
        print("❌ Valor inválido para depósito.")
        return

    conta["saldo"] += valor
    conta["extrato"].append((datetime.datetime.now(), f"Depósito: +R$ {valor:.2f}"))
    print("✅ Depósito realizado com sucesso!")

# Função para realizar saque (keyword-only)
def sacar(*, conta, valor, limite_saque=500, limite_saques_dia=3):
    if valor <= 0:
        print("❌ Valor inválido.")
    elif valor > conta["saldo"]:
        print("❌ Saldo insuficiente.")
    elif valor > limite_saque:
        print(f"❌ Valor excede o limite por saque: R$ {limite_saque:.2f}")
    elif conta["saques_realizados"] >= limite_saques_dia:
        print("❌ Limite diário de saques atingido.")
    else:
        conta["saldo"] -= valor
        conta["extrato"].append((datetime.datetime.now(), f"Saque: -R$ {valor:.2f}"))
        conta["saques_realizados"] += 1
        print("✅ Saque realizado com sucesso!")

# Função para exibir o extrato
def exibir_extrato(conta):
    print("\n🔍 EXTRATO BANCÁRIO:")
    if not conta["extrato"]:
        print("Não houve movimentações.")
    else:
        for data, operacao in conta["extrato"]:
            print(f"{data.strftime('%d/%m/%Y %H:%M:%S')} - {operacao}")
    print(f"\n💰 Saldo atual: R$ {conta['saldo']:.2f}")

# Selecionar conta do usuário por CPF
def selecionar_conta():
    cpf = input("Digite o CPF do titular: ").strip()
    contas_do_usuario = [conta for conta in contas if conta["usuario"]["cpf"] == cpf]

    if not contas_do_usuario:
        print("❌ Nenhuma conta encontrada para esse CPF.")
        return None

    print("Contas disponíveis:")
    for conta in contas_do_usuario:
        print(f"Conta {conta['numero']} - Agência {conta['agencia']}")

    numero = int(input("Digite o número da conta desejada: "))
    for conta in contas_do_usuario:
        if conta["numero"] == numero:
            return conta

    print("❌ Conta não encontrada.")
    return None

# Menu principal
def menu():
    while True:
        print("""
===== BANCO PYTHON =====
1 - Criar usuário
2 - Criar conta
3 - Depositar
4 - Sacar
5 - Exibir extrato
6 - Sair
""")
        opcao = input("Escolha uma opção: ").strip()

        if opcao == "1":
            criar_usuario()
        elif opcao == "2":
            criar_conta()
        elif opcao == "3":
            conta = selecionar_conta()
            if conta:
                depositar(conta)
        elif opcao == "4":
            conta = selecionar_conta()
            if conta:
                valor = float(input("Informe o valor do saque: R$ ").strip())
                sacar(conta=conta, valor=valor)
        elif opcao == "5":
            conta = selecionar_conta()
            if conta:
                exibir_extrato(conta)
        elif opcao == "6":
            print("🏦 Obrigado por usar o Banco Python!")
            break
        else:
            print("❌ Opção inválida. Tente novamente.")

# Iniciar o programa
menu()
