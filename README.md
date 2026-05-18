# pissoal_farma_aura

1. Problemas identificados no código original

O código original funciona, mas apresenta alguns problemas importantes:

Uso de String para representar status

O status do pedido é verificado usando textos como "novo" e "processado". Isso pode gerar erros caso alguém escreva o status de forma diferente, por exemplo "Novo", "PROCESSADO" ou "novo ".

Baixa organização da lógica

Toda a lógica de processamento está dentro do método processarPedido. Se novos status forem adicionados no futuro, esse método ficará cada vez maior e mais difícil de manter.

Dificuldade de manutenção

Sempre que surgir um novo tipo de status, será necessário alterar o when, aumentando o risco de quebrar algo que já funcionava.

Responsabilidades misturadas

A classe PedidoProcessor está decidindo o que fazer com cada status e também executando a ação. Isso fere o princípio da responsabilidade única, conhecido como SRP — Single Responsibility Principle.

Pouca escalabilidade

O código não está preparado para crescer de forma organizada caso apareçam novos status, como "cancelado", "em_entrega" ou "aguardando_pagamento".

2. Código refatorado em Kotlin
data class Pedido(
    val id: Int,
    val status: StatusPedido
)

enum class StatusPedido {
    NOVO,
    PROCESSADO,
    DESCONHECIDO
}

interface EstrategiaProcessamento {
    fun processar(pedido: Pedido)
}

class ProcessamentoPedidoNovo : EstrategiaProcessamento {
    override fun processar(pedido: Pedido) {
        println("Pedido em processamento: ${pedido.id}")
        Thread.sleep(2000) // Simulando processamento
    }
}

class ProcessamentoPedidoProcessado : EstrategiaProcessamento {
    override fun processar(pedido: Pedido) {
        println("Pedido já processado: ${pedido.id}")
    }
}

class ProcessamentoPedidoDesconhecido : EstrategiaProcessamento {
    override fun processar(pedido: Pedido) {
        println("Status desconhecido do pedido: ${pedido.id}")
    }
}

class PedidoProcessor {

    fun processarPedido(pedido: Pedido) {
        val estrategia = obterEstrategia(pedido.status)
        estrategia.processar(pedido)
    }

    private fun obterEstrategia(status: StatusPedido): EstrategiaProcessamento {
        return when (status) {
            StatusPedido.NOVO -> ProcessamentoPedidoNovo()
            StatusPedido.PROCESSADO -> ProcessamentoPedidoProcessado()
            StatusPedido.DESCONHECIDO -> ProcessamentoPedidoDesconhecido()
        }
    }
}

fun main() {
    val pedidoNovo = Pedido(1, StatusPedido.NOVO)
    val pedidoProcessado = Pedido(2, StatusPedido.PROCESSADO)
    val pedidoDesconhecido = Pedido(3, StatusPedido.DESCONHECIDO)

    val processor = PedidoProcessor()

    processor.processarPedido(pedidoNovo)
    processor.processarPedido(pedidoProcessado)
    processor.processarPedido(pedidoDesconhecido)
}
3. Explicação das mudanças realizadas

A primeira melhoria foi substituir os status em formato de texto por um enum class. Isso evita erros de digitação e torna o código mais seguro, pois agora os status possíveis ficam definidos em um único lugar.

Também foi criada uma interface chamada EstrategiaProcessamento, que define um comportamento padrão para todos os tipos de processamento de pedido. Cada status passou a ter sua própria classe responsável pelo processamento.

Com isso, foi aplicado o padrão de projeto Strategy, pois cada tipo de processamento foi separado em uma estratégia diferente.

Antes, a classe PedidoProcessor concentrava toda a lógica. Depois da refatoração, ela ficou responsável apenas por escolher a estratégia correta e executar o processamento.

Respostas das perguntas
1. Quais problemas você identificou no código original?

O código original tinha baixa organização, usava String para representar o status do pedido, misturava responsabilidades em uma única classe e dificultava a manutenção. Além disso, caso novos status fossem criados, seria necessário alterar diretamente o método processarPedido, deixando o código mais extenso e mais propenso a erros.

2. Como a refatoração melhorou a organização e legibilidade do código?

A refatoração separou a lógica de cada tipo de processamento em classes diferentes. Isso tornou o código mais organizado, pois cada classe possui uma responsabilidade clara. Além disso, o uso de enum class deixou os status mais fáceis de entender e mais seguros.

3. Quais padrões de design foram aplicados e por quê?

Foi aplicado o padrão Strategy, pois cada tipo de status do pedido passou a ter sua própria estratégia de processamento. Esse padrão foi usado para evitar um when muito grande dentro da classe principal e facilitar a adição de novos comportamentos no futuro.

Também foi aplicado o princípio SRP — Single Responsibility Principle, pois cada classe passou a ter apenas uma responsabilidade específica.

4. Como essas mudanças impactam a manutenção e escalabilidade do código no longo prazo?

As mudanças facilitam a manutenção porque, se for necessário alterar o comportamento de um status específico, basta modificar a classe correspondente. Além disso, se novos status forem adicionados, é possível criar novas estratégias sem alterar grande parte do código existente.

Isso torna o sistema mais escalável, organizado e menos sujeito a erros.

Q5. uais boas práticas foram aplicadas durante a refatoração?

Uso de enum class para evitar erros com textos digitados manualmente;
Separação de responsabilidades;
Aplicação do padrão Strategy;
Código mais modular;
Melhoria da legibilidade;
Redução do acoplamento;
Facilidade para manutenção futura;
Organização do código em classes com funções bem definidas.
