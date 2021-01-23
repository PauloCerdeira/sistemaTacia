<template>
  <div>
    <v-toolbar style="height: 75px; font-size: 20px; z-index: 1;">
      <v-row>
        <v-col cols="10" style="padding: 3px">
          <v-col cols="12" style="padding: 3px">
            <span><strong>Numero de vendas: </strong> {{ vendas.length }}</span>
          </v-col>
          <v-col cols="12" style="padding: 3px">
            <span
              ><strong>Valor total das Vendas: </strong>
              {{
                TotalDasVendas.toLocaleString("pt-br", {
                  style: "currency",
                  currency: "BRL",
                })
              }}
            </span>
          </v-col>
        </v-col>

        <v-col cols="2">
          <v-btn @click="openVenda = true">
            Nova Venda!
          </v-btn>
        </v-col>
      </v-row>
    </v-toolbar>

    <div :style="'overflow-y: scroll; max-height: ' + alturaTela + 'px'">
      <v-simple-table style="font-size: 20px">
        <thead>
          <tr>
            <th class="text-left">Numero da Venda:</th>
            <th class="text-left">Total da Venda:</th>
            <th style="width: 10%;" class="text-left"></th>
            <th style="width: 10%;" class="text-left"></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="venda in vendas" :key="venda.numero">
            <td>{{ venda.numero }}</td>
            <td>
              {{
                venda.total.toLocaleString("pt-br", {
                  style: "currency",
                  currency: "BRL",
                })
              }}
            </td>
            <td style="width: 10%;">
              <v-btn
                @click="
                  vendaDetalhada = venda;
                  vendaDetalhe = true;
                "
              >
                Ver detalhes
              </v-btn>
            </td>
            <td style="width: 10%;">
              <v-btn @click="removerVenda(venda)" elevation="7" rounded
                >excluir!</v-btn
              >
            </td>
          </tr>
        </tbody>
      </v-simple-table>

      <v-dialog v-model="openVenda" persistent max-width="800">
        <v-card>
          <v-simple-table style="min-height: 300px">
            <thead>
              <tr>
                <th style="width: 50px;" class="text-left">produto:</th>
                <th style="width: 50px;" class="text-left">valor:</th>
                <th style="width: 50px;" class="text-left">quantidade:</th>
                <th style="width: 50px;" class="text-left"></th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in novaVenda.itens" :key="item.numero">
                <td>{{ item.nome }}</td>
                <td>
                  {{
                    item.valorTotal.toLocaleString("pt-br", {
                      style: "currency",
                      currency: "BRL",
                    })
                  }}
                </td>
                <td>
                  <v-text-field
                    @change="
                      minMaxQuantidade(item);
                      calcularTotalItem(item);
                    "
                    @keyup="calcularTotalItem(item)"
                    @click="
                      minMaxQuantidade(item);
                      calcularTotalItem(item);
                    "
                    type="number"
                    v-model="item.quantidade"
                    :value="item.quantidade"
                  ></v-text-field>
                </td>
                <td>
                  <v-btn
                    @click="removeitemNovaVenda(item)"
                    elevation="7"
                    rounded
                    >excluir item</v-btn
                  >
                </td>
              </tr>
            </tbody>
          </v-simple-table>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="red" text @click="cancelarVenda()">
              Cancelar Venda.
            </v-btn>
            <v-btn
              :disabled="novaVenda.itens.length <= 0"
              color="green"
              text
              @click="finalizarVenda()"
            >
              Finalizar Venda!
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>

      <v-dialog v-model="vendaDetalhe" max-width="800">
        <v-card>
          <v-simple-table style="min-height: 300px">
            <thead>
              <tr>
                <th class="text-left">produto:</th>
                <th class="text-left">valor unitario:</th>
                <th class="text-left">quantidade:</th>
                <th class="text-left">valor total:</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in vendaDetalhada.itens" :key="item.idItem">
                <td>{{ item.nome }}</td>
                <td>
                  {{
                    item.valor.toLocaleString("pt-br", {
                      style: "currency",
                      currency: "BRL",
                    })
                  }}
                </td>
                <td>{{ item.quantidade }}</td>
                <td>
                  {{
                    item.valorTotal.toLocaleString("pt-br", {
                      style: "currency",
                      currency: "BRL",
                    })
                  }}
                </td>
              </tr>
            </tbody>
          </v-simple-table>
          <v-card-actions>
            <v-btn color="blue" @click="vendaDetalhe = false">
              Fechar!
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  created() {
    this.alturaTela = screen.height - 250;
    window.addEventListener("keydown", (e) => {
      if (e.key == "Enter") {
        this.openVenda = true;
        this.item = {
          idItem: 14,
          nome: "bolin de chocolate",
          valor: 7.5,
          quantidade: 1,
          valorTotal: 7.5,
        };
        this.addItemNovaVenda();
      } else if (e.key == "a") {
        this.openVenda = true;
        this.item = {
          idItem: 17,
          nome: "alvejante",
          valor: 16.0,
          quantidade: 1,
          valorTotal: 16.0,
        };
        this.addItemNovaVenda();
      }
    });
  },
  data: () => ({
    NumeroVenda: 0,
    alturaTela: 0,
    TotalDasVendas: 0.0,
    item: {
      idItem: 0,
      nome: "",
      quantidade: 0,
      valor: 0.0,
      valorTotal: 0.0,
    },
    novaVenda: {
      numero: 0,
      total: 0.0,
      itens: [],
    },
    openVenda: false,
    vendaDetalhe: false,
    vendaDetalhada: {},
    vendas: [
      // {
      //   numero: 1,
      //   total: 30.0,
      //   itens: [
      //     {
      //       idtem: 1,
      //       nome: `toddy`,
      //       quantidade: 1,
      //       valor: 10.0,
      //     },
      //     {
      //       idItem: 2,
      //       nome: `xuxu`,
      //       quantidade: 1,
      //       valor: 10.0,
      //     },
      //     {
      //       idItem: 3,
      //       nome: `bolacha`,
      //       quantidade: 1,
      //       valor: 10.0,
      //     },
      //   ],
      // },
      // {
      //   numero: 2,
      //   total: 50.0,
      //   itens: [
      //     {
      //       idtem: 1,
      //       nome: `nescau bau cereal`,
      //       quantidade: 1,
      //       valor: 30.0,
      //     },
      //     {
      //       idtem: 2,
      //       nome: `xuxu`,
      //       quantidade: 1,
      //       valor: 10.0,
      //     },
      //     {
      //       idtem: 3,
      //       nome: `bolacha`,
      //       quantidade: 1,
      //       valor: 10.0,
      //     },
      //   ],
      // },
    ],
  }),
  methods: {
    minMaxQuantidade(item) {
      if (item.quantidade > 99) {
        console.log("entrou if maior");
        item.quantidade = 99;
      } else if (item.quantidade <= 0) {
        console.log("entrou if menor");
        this.removeitemNovaVenda(item);
      }
    },
    addItemNovaVenda() {
      let itemExistente = false;
      if (this.novaVenda.itens.length == 0) {
        this.novaVenda.itens.push(this.item);
      } else {
        for (let i = 0; i <= this.novaVenda.itens.length - 1; i++) {
          if (this.novaVenda.itens[i].idItem == this.item.idItem) {
            itemExistente = true;
            this.novaVenda.itens[i].quantidade++;
            this.calcularTotalItem(this.novaVenda.itens[i]);
          }
        }
        if (!itemExistente) {
          this.novaVenda.itens.push(this.item);
        }
      }
    },
    removeitemNovaVenda(item) {
      this.novaVenda.itens.splice(this.novaVenda.itens.indexOf(item), 1);
    },
    finalizarVenda() {      
      this.openVenda = false;
      this.NumeroVenda++
      let totalDaVenda = 0.0;
      for (let i = 0; i <= this.novaVenda.itens.length - 1; i++) {
        totalDaVenda += this.novaVenda.itens[i].valorTotal;
      }
      this.novaVenda = {
        numero: this.NumeroVenda,
        total: totalDaVenda,
        itens: this.novaVenda.itens,
      };
      this.vendas.push(this.novaVenda);
      this.novaVenda = {
        numero: 0,
        total: 0.0,
        itens: [],
      };
      this.calcularTotalVendas();
    },
    cancelarVenda() {
      this.openVenda = false;
      (this.item = {
        idItem: 0,
        nome: "",
        quantidade: 0,
        valor: 0.0,
      }),
        (this.novaVenda = {
          numero: 0,
          total: 0.0,
          itens: [],
        });
    },
    removerVenda(vendaRemovida) {
      for (let i = 0; i < this.vendas.length; i++) {
        console.log(`entrou for`);
        if (this.vendas[i].numero == vendaRemovida.numero) {
          console.log(`entrou if`);
          console.log(this.vendas[i].numero);
          this.vendas.splice(i, 1);
        }
      }
      this.calcularTotalVendas();
    },
    calcularTotalItem(item) {
      item.valorTotal = item.quantidade * item.valor;
      this.calcularTotalVenda();
    },
    calcularTotalVenda() {
      let totaldasvenda = 0.0;
      for (let i = 0; i < this.novaVenda.itens.length - 1; i++) {
        totaldasvenda += this.novaVenda.itens[i].valorTotal;
      }
      this.novaVenda.total = totaldasvenda;
    },
    calcularTotalVendas() {
      let totaldasvendas = 0.0;

      for (let i = 0; i < this.vendas.length; i++) {
        console.log(this.vendas[i].total);
        totaldasvendas += this.vendas[i].total;
      }
      this.TotalDasVendas = totaldasvendas;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

</style>
