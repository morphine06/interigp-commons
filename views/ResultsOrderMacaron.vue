<template>
  <div>
    <div class="row">
      <div class="col-md-5 offset-md-7">
        <div>
          Nombre de macaron Or : {{ row_or.or_or | formatChiffreSimple }}
        </div>
        <div>
          Nombre de macaron Argent :
          {{ row_or.or_argent | formatChiffreSimple }}
        </div>
        <div>
          Nombre de macaron Excellence :
          {{ row_or.or_excellence | formatChiffreSimple }}
        </div>
      </div>
    </div>
    <div class="row mt-3">
      <div class="col">
        <table class="table table-striped table-bordered">
          <thead>
            <tr>
              <th>Tarifs des macarons :</th>
              <th>Prix HT pour mille (port non compris)</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>De 0 à 99.999 unités</td>
              <td>25 €</td>
            </tr>
            <tr>
              <td>De 100.000 à 499.999 unités</td>
              <td>20 €</td>
            </tr>
            <tr>
              <td>Plus de 500.000 unités</td>
              <td>15 €</td>
            </tr>
          </tbody>
        </table>

      </div>
    </div>
    <!-- <div class="row mt-3">
      <div class="col-md-7">
        <div>
          Sous-total HT :
          {{ (row_or.or_or + row_or.or_argent + row_or.or_excellence) / 1000 }}
          lot(s) de 1000 x
          {{
            row_or.or_price_macaron
              ? $options.filters.formatPriceDivisePar100(
                  row_or.or_price_macaron
                )
              : $options.filters.formatPriceDivisePar100(
                  row_or.yp_macarons_price
                )
          }}
          = {{ row_or.or_price_ht | formatPriceDivisePar100 }} HT
        </div>
        <div v-if="from == 'candidats'">
          Frais de port :
          <span @click="showFraisDePort = false" class="color-red pointer"
            >{{ row_or.or_fraisport }} €</span
          >
        </div>
        <div v-else>
          <m-form-text
            class="ms-2"
            type="number"
            label="Frais de port HT"
            :name="$Utils.randomstring('or_fraisport')"
            v-model="row_or.or_fraisport"
            @input="calculTotal"
          ></m-form-text>
          <h5>
            Administrateur : Saisissez un montant pour les frais de port et
            cliquez sur le sélecteur Envoi du mail à côté du bouton Valider
          </h5>
        </div>
      </div>
      <div class="col-md-5">
        <div class="frame bg-gray rounded-0">
          <div class="d-flex align-items-center">
            <div>HT :</div>
            <h5 class="color-red ms-2">
              {{ totaux.ht | formatPriceDivisePar100 }}
            </h5>
            &nbsp;(+ frais de port :
            {{ totaux.fraisport | formatPriceDivisePar100 }})
          </div>
          <div class="d-flex align-items-center">
            <div>TVA :</div>
            <h5 class="color-red ms-2">
              {{ totaux.tva | formatPriceDivisePar100 }}
            </h5>
          </div>
          <div class="d-flex align-items-center">
            <div>Total TTC :</div>
            <h2 class="color-red ms-2">
              {{ totaux.ttc | formatPriceDivisePar100 }} TTC
            </h2>
          </div>
          <div class="color-red" v-if="row_or.or_fraisport == 0">
            (Frais de port en sus indiqués sur la facture à venir)
          </div>
        </div>
      </div>
    </div> -->
  </div>
</template>

<script>
export default {
  name: "resultsOrderMacaron",
  components: {},
  props: {
    row_or: Object,
    from: String,
  },
  data() {
    return {
      totaux: { ht: 0, ttc: 0, tva: 0 },
      showFraisDePort: false,
    };
  },
  watch: {
    row_or(v) {
      v.or_fraisport = v.or_fraisport / 100;
      this.calculTotal();
    },
  },
  async mounted() {
    this.calculTotal();
  },
  methods: {
    calculTotal() {
      let totaux = { ht: 0, ht: 0, ttc: 0, tva: 0, fraisport: 0 };
      let tt =
        this.row_or.or_or + this.row_or.or_argent + this.row_or.or_excellence;
      if (this.row_or.or_price_macaron) {
        totaux.ht = (tt / 1000) * this.row_or.or_price_macaron;
      } else {
        totaux.ht = (tt / 1000) * this.row_or.yp_macarons_price;
      }
      // if (this.row_or.or_fraisport) totaux.ht += this.row_or.or_fraisport*100;
      totaux.fraisport = this.row_or.or_fraisport * 100;
      totaux.tva = (totaux.ht + totaux.fraisport) * (this.row_or.yp_tva / 100);
      totaux.ttc = totaux.tva + (totaux.ht + totaux.fraisport);
      this.totaux = totaux;

      this.row_or.or_price_ht = totaux.ht;
      this.row_or.or_price_ttc = totaux.ttc;
      this.row_or.or_price_tva = totaux.tva;

      this.$emit("changetotalOrder", totaux);
    },
  },
};
</script>

<style lang="scss"
  scoped></style>
