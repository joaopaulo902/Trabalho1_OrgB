# Implementação do pipeline

Descreverei brevemente como foi implementada cada instrução


## jalr rd, rs1, imm12

Essa foi a mais difícil porque não havia sido implementado previamente
o datapath pra jump incondicional. Exigiu mudanças no controle e um sinal de controle a mais: o "jump".

Para a implementação foi colocado um multiplexador entre "OldPc" e "OldOldPc",
permitindo introduzir o rs1 como operando do somador de endereços. Mais tarde eu vi que era
necessário também outro caminho de imediato que não recebesse shift left e sabendo disso essa lógica
de usar exatamente esse caminho talvez não fizesse mais tanto sentido; foi, todavia, a implementação final
do caminho PC = rs1 + imm.

Além disso, para salvar o PC + 4, foi feito um puxadinho no incrementador pra guardar num registrador
que passa pra outro, e outro... Até chegar na etapa de write-back.
Definitivamente, não é o jeito mais elegante, mas funciona bem e não aumenta em nada o caminho crítico.

## blt rs1, rs2, label

Pra essa foi feito um puxadinho + registrador saindo do registrador que segura o funct3 do alu_control.
Daí, tirando da ULA outro flag (de negativo), é feita uma seleção baseada no funct3 + sinal de Branch + flag da ULA
usando dois AND3.

Se fosse necessário implementar todos os branches, seria fácil estender essa lógica para seleção de tipo de comparação.
Podendo inclusive ser modularizada, como foi o alu_control.
Sendo suficiente (até onde eu entendo) a flag de negativo para quaisquer implementações.


p.s.: Não foi exigido alterar o gerador de sinais de controle porque os sinais são exatamente iguais aos de um beq.


## slli/sltiu rd, rs1, imm

Essas foram tranquilas de implementar (com exceção da decodificação do opcode que tinha que criar, tarefa bem simples) 
porque era simplesmente adicionar uma unidade de shift e um comparador, respectivamente e passar como resultado da ULA
os sinais, sinal do < do comparador no caso do sltiu.

Houve também necessidade de mudar o alu_control para incluir as novas saídas baseadas nos funct3 de cada instrução.
Ficou 100% hardcoded porque a implementação estava um pouco confusa no tocante ao que dava ou não dava pra mexer sem quebrar o que
já funcionava.


# Nota

Os hazards do pipeline não foram resolvidos, então todos os testes exigem inspeção manual do código para verificar sua existência e, em
caso positivo, inserir alguns NOPs artificialmente depois de cada operação problemática.
