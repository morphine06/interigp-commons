<template>
  <div>
    <div class="frame">
      <div class="row mb-3">
        <div class="col-md-5">
          <h4>Entreprise</h4>
          <m-form-text class="mb-2"
            label="Nom de l'entreprise ou de l'exploitation *"
            :name="$Utils.randomstring('pa_society')"
            v-model="row_pa.pa_society"></m-form-text>
          <m-form-text class="mb-1"
            label="Adresse *"
            :name="$Utils.randomstring('pa_address1')"
            v-model="row_pa.pa_address1"></m-form-text>
          <m-form-text class="mb-2"
            :name="$Utils.randomstring('pa_address2')"
            v-model="row_pa.pa_address2"></m-form-text>
          <m-form-text class="mb-2"
            :name="$Utils.randomstring('pa_address3')"
            v-model="row_pa.pa_address3"></m-form-text>
          <div class="row mb-2">
            <div class="col-md-6">
              <m-form-text label="Code postal *"
                :name="$Utils.randomstring('pa_zip')"
                v-model="row_pa.pa_zip"></m-form-text>
            </div>
            <div class="col-md-6">
              <m-form-text label="Ville *"
                :name="$Utils.randomstring('pa_city')"
                v-model="row_pa.pa_city"></m-form-text>
            </div>
          </div>
          <m-form-text label="Site web"
            :name="$Utils.randomstring('pa_web')"
            v-model="row_pa.pa_web"></m-form-text>
        </div>
        <div class="col-md-6 offset-md-1">
          <h4>Coordonnées</h4>
          <m-form-select class="mb-2"
            label="Civilité *"
            :items="$store.state.items_civility"
            :name="$Utils.randomstring('pa_civility')"
            v-model="row_pa.pa_civility"></m-form-select>
          <div class="row mb-2">
            <div class="col-md-6">
              <m-form-text label="Prénom *"
                :name="$Utils.randomstring('pa_firstname')"
                v-model="row_pa.pa_firstname"></m-form-text>
            </div>
            <div class="col-md-6">
              <m-form-text label="Nom *"
                :name="$Utils.randomstring('pa_name')"
                v-model="row_pa.pa_name"></m-form-text>
            </div>
          </div>
          <m-form-text class="mb-2"
            label="Fonction *"
            :name="$Utils.randomstring('pa_fonction')"
            v-model="row_pa.pa_fonction"></m-form-text>
          <div class="row mb-2">
            <div class="col-md-6">
              <m-form-text label="Téléphone fixe *"
                :name="$Utils.randomstring('pa_phone_fix')"
                v-model="row_pa.pa_phone_fix"></m-form-text>
            </div>
            <div class="col-md-6">
              <m-form-text label="Téléphone mobile"
                :name="$Utils.randomstring('pa_phone_mobile')"
                v-model="row_pa.pa_phone_mobile">
              </m-form-text>
            </div>
          </div>

          <m-form-combobox v-model="row_pa.center1"
            style="max-width: 870px"
            label="Votre interprofession principale"
            :store-url="$config.server_url + '/backoffice/1.0/centers'"
            item-value="ce_id"
            :item-text="$options.filters.formatCenterName"
            :store-params="{
              sort: 'ce_name ASC',
            }"></m-form-combobox>
          <m-form-combobox v-model="row_pa.center2"
            style="max-width: 870px"
            label="Votre interprofession secondaire"
            :store-url="$config.server_url + '/backoffice/1.0/centers'"
            item-value="ce_id"
            :item-text="$options.filters.formatCenterName"
            :store-params="{
              sort: 'ce_name ASC',
            }"></m-form-combobox>
        </div>
      </div>
    </div>
    <div class="frame">
      <div class="d-flex justify-content-between">
        <m-form-checkbox class="mb-2"
          label="<span class='fw-bold'>Je souhaite être candidat</span>"
          :name="$Utils.randomstring('pa_candidat')"
          v-model="row_pa.pa_candidat"
          @change="checkboxCandidat"
          :disabled="from !== 'backoffice' &&
            !$dayjs().isBetween(
              row_pa.yearpreferences.yp_start_inscription_date,
              row_pa.yearpreferences.yp_end_inscription_date_candidate,
              'day',
              '[]'
            )
            "></m-form-checkbox>
        <div class="d-flex align-items-center"
          v-if="from === 'backoffice' && $Utils.isAdmin()">
          <div v-if="row_pa.pa_candidat_valide"
            class="me-2">
            Candidat validé
          </div>
          <div v-else
            class="me-2">Candidat non validé</div>
          <label class="switch">
            <input v-model="row_pa.pa_candidat_valide"
              type="checkbox"
              @change="devalidCandidat" />
            <span class="slider round"></span>
          </label>
        </div>
      </div>
      <div v-if="row_pa.pa_candidat">
        <div class="row">
          <div class="col-md-5">
            <div class="d-flex mb-3">
              <h4>Adresse de facturation</h4>
              <div>
                <button class="btn btn-primary ms-4 btn-sm"
                  @click="copyAddress">
                  Copier
                </button>
              </div>
            </div>
            <div>
              <m-form-text class="mb-2"
                label="Nom de l'entreprise ou de l'exploitation *"
                :name="$Utils.randomstring('pa_society_fac')"
                v-model="row_pa.pa_society_fac"></m-form-text>
              <m-form-text class="mb-1"
                label="Adresse *"
                :name="$Utils.randomstring('pa_address1_fac')"
                v-model="row_pa.pa_address1_fac"></m-form-text>
              <m-form-text class="mb-2"
                :name="$Utils.randomstring('pa_address2_fac')"
                v-model="row_pa.pa_address2_fac"></m-form-text>
              <m-form-text class="mb-2"
                :name="$Utils.randomstring('pa_address3_fac')"
                v-model="row_pa.pa_address3_fac"></m-form-text>
              <div class="row mb-2">
                <div class="col-md-6">
                  <m-form-text label="Code postal *"
                    :name="$Utils.randomstring('pa_zip_fac')"
                    v-model="row_pa.pa_zip_fac"></m-form-text>
                </div>
                <div class="col-md-6">
                  <m-form-text label="Ville *"
                    :name="$Utils.randomstring('pa_city_fac')"
                    v-model="row_pa.pa_city_fac"></m-form-text>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="frame">
      <div class="d-flex justify-content-between">
        <m-form-checkbox class="mb-2"
          label="<span class='fw-bold'>Je souhaite être juré</span>"
          :name="$Utils.randomstring('pa_jure')"
          v-model="row_pa.pa_jure"
          @input="checkboxJure"
          :disabled="from !== 'backoffice' &&
            !$dayjs().isBetween(
              row_pa.yearpreferences.yp_start_inscription_date,
              row_pa.yearpreferences.yp_end_inscription_date_jure,
              'day',
              '[]'
            )
            "></m-form-checkbox>

        <div class="d-flex align-items-center"
          v-if="from === 'backoffice' && $Utils.isAdmin()">
          <div v-if="row_pa.pa_jure_valide"
            class="me-2">Juré validé</div>
          <div v-else
            class="me-2">Juré non validé</div>
          <label class="switch">
            <input :disabled="disabledJure"
              v-model="row_pa.pa_jure_valide"
              type="checkbox" />
            <span class="slider round"></span>
          </label>
        </div>
      </div>
      <div v-if="row_pa.pa_jure">
        <h4>Plus d'informations</h4>
        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Est-ce votre première participation ?
          </div>
          <m-form-select class="width200"
            :name="$Utils.randomstring('pa_firstparticipation')"
            v-model="row_pa.pa_firstparticipation"
            :items="$store.state.items_boolean_int"></m-form-select>
        </div>
        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Dans quelle catégorie vous situez-vous ?
          </div>
          <m-form-select class="width300"
            :name="$Utils.randomstring('pa_typejure')"
            v-model="row_pa.pa_typejure"
            :items="$store.state.items_typejure"></m-form-select>
          <div class="d-md-flex align-items-center ms-3 mb-2"
            v-if="row_pa.pa_typejure === 9">
            <div class="me-1">Précisez :</div>
            <m-form-text :name="$Utils.randomstring('pa_typejure_other')"
              v-model="row_pa.pa_typejure_other"></m-form-text>
          </div>
        </div>
        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Avez-vous suivi des cours de dégustation ?
          </div>
          <m-form-select class="width200"
            :name="$Utils.randomstring('pa_coursdegustation')"
            v-model="row_pa.pa_coursdegustation"
            :items="$store.state.items_boolean_int"></m-form-select>
        </div>

        <div class="d-md-flex align-items-center mb-2 d-flex">
          <div class="fw-bold me-4 width360">
            Habilitation délivrée par une interprofession
          </div>
          <m-form-combobox v-model="row_pa.center3"
            class="flex-fill"
            label=""
            :store-url="$config.server_url + '/backoffice/1.0/centers'"
            item-value="ce_id"
            :item-text="$options.filters.formatCenterName"
            :store-params="{
              sort: 'ce_name ASC',
            }"></m-form-combobox>
        </div>

        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Nuitée la veille du concours: souhaitez-vous réserver le logement
            proposé dans le programme
          </div>
          <m-form-select class="width200"
            :name="$Utils.randomstring('pa_reserverlogement')"
            v-model="row_pa.pa_reserverlogement"
            :items="$store.state.items_boolean_int"></m-form-select>
        </div>

        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Nuitée la veille du concours: accepteriez-vous de partager un
            appartement avec d'autres personnes (chambre séparée) ? *
          </div>
          <m-form-select class="width200"
            :name="$Utils.randomstring('pa_reserverlogement_partage')"
            v-model="row_pa.pa_reserverlogement_partage"
            :items="$store.state.items_boolean_int"></m-form-select>
        </div>

        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Soirée la veille du concours: souhaitez-vous participer ?
          </div>
          <m-form-select class="width200"
            :name="$Utils.randomstring('pa_soireeveilleconcours')"
            v-model="row_pa.pa_soireeveilleconcours"
            :items="$store.state.items_boolean_int"></m-form-select>
        </div>

        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Déjeuner après le concours: souhaitez-vous participer?
          </div>
          <m-form-select class="width200"
            :name="$Utils.randomstring('pa_dejeunerapresconcours')"
            v-model="row_pa.pa_dejeunerapresconcours"
            :items="$store.state.items_boolean_int"></m-form-select>
        </div>

        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Soirée et nuitée de la veille, déjeuner après le concours,
            souhaitez-vous venir accompagné ?
          </div>
          <m-form-select class="width200"
            :name="$Utils.randomstring('pa_dejeunerapresconcours_accompagne')"
            v-model="row_pa.pa_dejeunerapresconcours_accompagne"
            :items="$store.state.items_boolean_int"></m-form-select>
        </div>

        <div class="d-md-flex align-items-center mb-2">
          <div class="fw-bold me-4 width360">
            Nom et Prénom de la l'accompagnant :
          </div>
          <m-form-text label=""
            :name="$Utils.randomstring('pa_dejeunerapresconcours_name')"
            style="min-width: 200px"
            v-model="row_pa.pa_dejeunerapresconcours_name"></m-form-text>
        </div>

        <div>
          <p class="mb-1 mt-3">
            Je déclare sur l'honneur que mes liens directs ou indirects avec les
            entreprises, établissements, organisations professionnelles ou
            associations dont les activités, produits ou intérêts pouvant
            concerner les vins présentés au concours sont les suivants :
          </p>
          <div class="d-md-flex align-items-end ps-sm-4">
            <m-form-radio class="width150"
              :name="$Utils.randomstring('pa_liensexistant')"
              id="pa_liensexistant"
              v-model="row_pa.pa_liensexistant"
              :items="$store.state.items_liensJure"></m-form-radio>

            <m-form-text class="width250"
              inputGroupClass="input-group-sm"
              :name="$Utils.randomstring('pa_liensexistant_more')"
              v-model="row_pa.pa_liensexistant_more"></m-form-text>
          </div>
        </div>
        <hr />
        <div>
          <m-form-checkbox
            label="Je reconnais avoir lu et compris le consentement au traitement de mes données personnelles, et j'accepte que mes données soient utilisées aux fins décrites dans le présent texte."
            :name="$Utils.randomstring('pa_jure_consentement')"
            v-model="row_pa.pa_jure_consentement"
            :labelwhitespace="true"></m-form-checkbox>
          <a class="text-blue pointer"
            @click.stop="showConsentement">Plus d'infos sur le traitement de mes données personnelles</a>
          <m-form-checkbox class="mt-2"
            label="Je ne souhaite pas que mes données personnelles soient conservées. Je reconnais que je ne pourrais donc pas participer au concours des vins du Sud-ouest."
            :name="$Utils.randomstring('noConsentement')"
            v-model="noConsentement"
            :labelwhitespace="true"></m-form-checkbox>
        </div>
        <!--         <hr />
        <h4>Centres de dégustations</h4>
        <p class="fw-bold">Choisissez votre centre de dégustation :</p>
        <m-form-radio
          class="ps-5"
          :name="$Utils.randomstring('pa_centredegustation')"
          id="pa_centredegustation"
          v-model="row_pa.pa_centredegustation"
          :items="items_centredegustation"
        ></m-form-radio> -->

        <!-- <m-form-checkbox
          class="mt-3 ps-5"
          label="Je souhaite participer aussi à la dégustation du vignoble de Gaillac le 42 mars 2002"
          :name="$Utils.randomstring('pa_gaillac')"
          v-model="row_pa.pa_gaillac"
          
        ></m-form-checkbox> -->
      </div>
    </div>
    <m-confirm-dialog v-model="confirmJureValide"
      text="Vous allez valider le juré, confirmez-vous cette action ?"
      title="Confirmation"
      @canceled="cancelJureValide()"
      @confirmed="saveCandidat()"></m-confirm-dialog>
    <m-confirm-dialog v-model="confirmDevalidDialog"
      text="Voulez vous mettre à 'Non validé' tous les vins de ce candidat ? Attention cette action entraine peut être que vous devez créer un avoir manuellement et que ces vins ne participeront pas au Coucours"
      title="Confirmation"
      btnOkTxt="Oui"
      :threeBtn="true"
      btn3Txt="Non"
      @btn3Action="DevalidCandidatNon()"
      @canceled="DevalidCandidatCancel()"
      @confirmed="DevalidCandidatOui()"></m-confirm-dialog>
    <m-message-dialog v-model="dialogErr"
      title="Erreur"
      :text="dialogErrTxt"></m-message-dialog>
    <m-message-dialog v-model="showModalConsentement"
      title="Confirmation de consentement au traitement de vos données personnelles"
      :text="consentementTxt"
      :width="1000"
      color="#3eb2dc"></m-message-dialog>
  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";

export default {
  name: "FormCandidat",
  components: {},
  props: {
    pa_id: Number,
    row_pa_tocopy: Number,
    keyload: Number,
    from: String,
    signup: Boolean,
    row_yp: Object,
  },
  data() {
    return {
      dialogErr: false,
      dialogErrTxt: "",
      row_pa: {
        pa_society_fac: "",
        pa_address1_fac: "",
        pa_address2_fac: "",
        pa_address3_fac: "",
        pa_city_fac: "",
        pa_zip_fac: "",
        yearpreferences: {},
      },
      jureValide: false,
      confirmJureValide: false,
      disabledJure: false,
      items_centredegustation: [
        { value: 1, text: "Vignoble 1" },
        { value: 2, text: "Vignoble 2" },
        { value: 3, text: "Vignoble 3" },
        { value: 4, text: "Vignoble 4" },
        { value: 5, text: "Vignoble 5" },
      ],
      // row_yp2: {},
      confirmDevalidDialog: false,
      devalidWinesCandidat: false,
      noConsentement: false,
      showModalConsentement: false,
      consentementTxt: `<p class="text-justify">En postulant en tant que juré, vous nous autorisez à collecter vos données personnelles et à les utiliser dans le cadre de l’organisation du concours des vins du sud-ouest.<br/>
Vos données à caractère personnel font l’objet d’un traitement automatisé destiné à l’IVSO (Centre INRA – CS52637 – 31321 Castanet-Tolosan cedex). Nous collectons et traitons les données suivantes : vos nom, prénom(s), âge, adresse postale, adresse email ainsi que les liens directs ou indirects avec les entreprises, établissements, organisations professionnelles ou que vous avez déclaré ci-dessus (ci-après vos « Données »). Vos Données seront utilisées pour vous contacter, traiter votre candidature et la bonne organisation du concours des vins du Sud-ouest.<br/>
Il est rappelé que dans le cadre du traitement de vos Données, et conformément aux dispositions aux dispositions du règlement (UE) 2016/679 du Parlement Européen et du Conseil du 27 avril 2016 applicable à compter du 25 mai 2018 (ci-après « RGPD ») et de la Loi n°78-17 du 6 janvier 1978 modifiée, relative à l’informatique, aux fichiers et aux libertés, vous avez le droit de savoir si nous détenons vos Données et, si c’est le cas, vous disposez des droits suivants dans les conditions précisées dans le RGPD: </p>
<ul>
<li>
Droit d’accès (article 15 du RGPD);
</li>
<li>
Droit de rectification (article 16 du RGPD);
<li>
Droit à l’effacement (article 17 du RGPD);
</li>
<li>
Droit à la limitation du traitement (article 18 du RGPD);
</li>
<li>
Droit à la portabilité (article 20 du RGPD);
</li>
<li>
Droit d’opposition (article 21 du RGPD).
</li>

</ul>
<p class="text-justify">
Tous les droits énumérés ci-dessus sont personnels et ne peuvent être exercés que par la personne ayant communiqué ses Données auprès l’IVSO, sur présentation d’un justificatif d’identité. <br/>
Pour exercer vos droits ou pour formuler toute autre demande concernant le traitement de vos Données, vous pouvez contacter directement l’IVSO par le moyen suivant dpo@france-sudouest.com.<br/>
Vous avez également la possibilité de saisir l’autorité de contrôle, la CNIL : https://www.cnil.fr
Aux fins des présentes, vos Données pourront être conservées par l’IVSO pendant un délai maximum de 3 ans après la fin du traitement.
Dans le cadre du traitement décrit aux présentes, sont prises toutes les mesures techniques, organisationnelles et de sécurité appropriée contre tout accès non autorisé, altération, divulgation, destruction et perte de vos Données, lesquelles sont hébergées dans des centres de données localisés au sein de l’Union Européenne pour toute la durée des traitements. Sauf disposition législative, règlementaire ou décision de justice contraire, l’accès à vos Données sera restreint aux collaborateurs habilités au sein de l’IVSO et le cas échéant à leurs prestataires, lorsque ces derniers ont besoin de les consulter et/ou de les utiliser pour les finalités décrites ci-dessus.</p>`,
    };
  },
  watch: {
    row_pa_tocopy: function (v) {
      this.loadParticipation();
    },
    pa_id: function (v) {
      this.loadParticipation();
    },
  },
  async mounted() {
    await this.loadParticipation();
    // console.log("this.row_yp", this.row_yp, this.row_pa.yearpreferences);
    // this.row_yp2 = this.row_yp ? this.row_yp : this.row_pa.yearpreferences;
  },
  methods: {
    async loadParticipation() {
      // console.log('this.pa_id', this.pa_id);
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      let params = {};
      // console.log("this.route, from", this.route, this.from);
      if (!this.signup) {
        if (this.pa_id == -1) params = {};
        if (this.row_pa_tocopy) params.lo_id = this.row_pa_tocopy;
        let response = await this.$axios.get(
          this.$config.server_url +
          "/" +
          route +
          "/1.0/participations/" +
          this.pa_id,
          { params }
        );
        this.row_pa = response.data.data;
        // console.log('this.row_pa.yearpreferences', this.row_pa.yearpreferences);
        // let diff = this.$dayjs().isBetween(
        //   this.row_pa.yearpreferences.yp_start_inscription_date,
        //   this.row_pa.yearpreferences.yp_end_inscription_date_candidate,
        //   "day"
        // );
        // console.log('diff', diff,this.row_pa.yearpreferences.yp_end_inscription_date_candidate);
        // console.log('this.row_pa1', this.row_pa);
        this.jureValide = this.row_pa.pa_jure_valide;
        this.$emit("participationLoaded", this.row_pa);
      }
    },
    devalidCandidat() {
      if (!this.row_pa.pa_candidat_valide) this.confirmDevalidDialog = true;
      if (this.row_pa.pa_candidat_valide)
        this.row_pa.revalidWinesCandidat = true;
    },
    DevalidCandidatOui() {
      this.confirmDevalidDialog = false;
      this.row_pa.devalidWinesCandidat = true;
      this.row_pa.pa_candidat_valide = false;
    },
    DevalidCandidatNon() {
      this.confirmDevalidDialog = false;
      this.row_pa.devalidWinesCandidat = false;
      this.row_pa.pa_candidat_valide = false;
    },
    DevalidCandidatCancel() {
      this.confirmDevalidDialog = false;
      this.row_pa.devalidWinesCandidat = false;
      this.row_pa.pa_candidat_valide = true;
    },
    copyAddress() {
      this.row_pa.pa_society_fac = this.row_pa.pa_society
        ? this.row_pa.pa_society
        : "";
      this.row_pa.pa_address1_fac = this.row_pa.pa_address1
        ? this.row_pa.pa_address1
        : "";
      this.row_pa.pa_address2_fac = this.row_pa.pa_address2
        ? this.row_pa.pa_address2
        : "";
      this.row_pa.pa_address3_fac = this.row_pa.pa_address3
        ? this.row_pa.pa_address3
        : "";
      this.row_pa.pa_city_fac = this.row_pa.pa_city ? this.row_pa.pa_city : "";
      this.row_pa.pa_zip_fac = this.row_pa.pa_zip ? this.row_pa.pa_zip : "";
    },
    checkboxCandidat(val) {
      this.row_pa.pa_candidat = val;
      if (this.row_pa.pa_candidat) this.row_pa.pa_candidat_valide = true;
      else this.row_pa.pa_candidat_valide = false;
    },
    checkboxJure() {
      if (this.row_pa.pa_jure) this.disabledJure = false;
      else this.disabledJure = true;
    },
    showConsentement() {
      this.showModalConsentement = true;
    },
    tryToSaveWin(login, tabErrPassword) {
      if (this.noConsentement && this.row_pa.pa_jure) {
        this.dialogErrTxt =
          "<span class='fw-bold'>Vous ne souhaitez pas que l'on enregistre vos données personnelles, vous ne pouvez donc pas continuer.</span><br>";
        this.dialogErr = true;
        return;
      }
      // console.log("je passe", tabErrPassword);
      this.row_pa.login = login;
      this.row_pa.lo_id = login.lo_id;
      let err = [];
      let fieldRequired = [
        {
          field: "pa_society",
          text: "Nom de l'entreprise",
        },
        { field: "pa_civility", text: "Civilité" },
        { field: "pa_name", text: "Nom" },
        { field: "pa_fonction", text: "Fonction" },
        { field: "pa_phone_fix", text: "Téléphone fixe" },
        { field: "pa_firstname", text: "Prénom" },
        { field: "pa_address1", text: "Adresse" },
        { field: "pa_zip", text: "Code postal" },
        { field: "pa_city", text: "Ville" },
      ];
      for (let ifi = 0; ifi < fieldRequired.length; ifi++) {
        const field = fieldRequired[ifi];
        if (!this.row_pa[field.field]) err.push(field);
      }
      if (!this.row_pa.login.lo_login)
        err.push({ text: "Login (en bas de la fenêtre)" });
      if (
        !this.row_pa.login.lo_pass &&
        (this.signup || !this.row_pa.login || !this.row_pa.login.lo_id)
      )
        err.push({ text: "Mot de passe" });
      if (tabErrPassword && tabErrPassword.length > 0) {
        for (let i = 0; i < tabErrPassword.length; i++) {
          err.push({ text: tabErrPassword[i] });
        }
      }
      // champs obligatoire du candidat
      if (this.row_pa.pa_candidat) {
        if (!this.row_pa.pa_society_fac)
          err.push({ text: "Adresse de facturation : nom de la société" });
        if (!this.row_pa.pa_address1_fac)
          err.push({ text: "Adresse de facturation : adresse" });
        if (!this.row_pa.pa_zip_fac)
          err.push({ text: "Adresse de facturation : code postal" });
        if (!this.row_pa.pa_city_fac)
          err.push({ text: "Adresse de facturation : ville" });
      }
      // champs obligatoire du juré
      if (this.row_pa.pa_jure) {
        if (!this.row_pa.pa_firstparticipation)
          err.push({ text: "Juré : première participation" });
        if (!this.row_pa.pa_typejure) err.push({ text: "juré : catégorie" });
        if (this.row_pa.pa_typejure === 9 && !this.row_pa.pa_typejure_other)
          err.push({ text: "Juré catégorie : précisez" });
        if (!this.row_pa.pa_coursdegustation)
          err.push({ text: "Juré : cours de dégustation" });
        if (!this.row_pa.pa_liensexistant)
          err.push({ text: "Juré : lien existant" });
        if (
          (this.row_pa.pa_liensexistant === "1" ||
            this.row_pa.pa_liensexistant === 1) &&
          !this.row_pa.pa_liensexistant_more
        )
          err.push({ text: "Juré lien existant : précisez" });
        if (!this.row_pa.pa_jure_consentement) {
          err.push({ text: "Consentement sur mes données personnelles" });
        }
      }
      // console.log("this.row_pa", this.row_pa);
      if (!this.row_pa.pa_candidat && !this.row_pa.pa_jure) {
        err.push({
          text: "<span class=''>Vous devez choisir d'être candidat ou juré ou les deux</span>",
        });
      }
      // console.log("err", err);
      if (err.length) {
        this.dialogErrTxt =
          "<span class='fw-bold'>Les champs suivants sont obligatoires : </span><br>";
        for (let ierr = 0; ierr < err.length; ierr++) {
          const error = err[ierr];
          this.dialogErrTxt += "- " + error.text + " <br>";
        }
        this.dialogErr = true;
        return;
      }
      // confirme juré validé
      if (!this.jureValide && this.row_pa.pa_jure_valide) {
        this.confirmJureValide = true;
        this.jureValideConfirmed = true;
      } else {
        this.saveCandidat();
      }
    },
    cancelJureValide() {
      this.confirmJureValide = false;
      this.jureValideConfirmed = false;
    },
    trimFields() {
      let tabField = [
        "pa_name",
        "pa_firstname",
        "pa_society",
        "pa_zip",
        "pa_city",
        "pa_web",
        "pa_phone_mobile",
        "pa_phone_fix",
      ];
      for (let i = 0; i < tabField.length; i++) {
        const field = tabField[i];
        if (this.row_pa[field]) this.row_pa[field] = this.row_pa[field].trim();
      }
    },
    async saveCandidat() {
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      this.confirmJureValide = false;
      if (this.jureValideConfirmed) this.row_pa.sendEmailToJureValide = true;
      await this.trimFields();
      //this.row_pa.pa_email = this.row_pa.pa_email.trim();

      let response;
      if (this.row_pa.pa_id) {
        response = await this.$axios.put(
          this.$config.server_url +
          "/" +
          route +
          "/1.0/participations/" +
          this.row_pa.pa_id,
          this.row_pa
        );
      } else {
        this.row_pa.pa_cgu_date = "0000-00-00 00:00:00";
        this.row_pa.pa_year = this.$store.state.year;
        if (this.signup) {
          response = await this.$axios.post(
            this.$config.server_url + "/candidats/1.0/signup",
            this.row_pa
          );
        } else {
          response = await this.$axios.post(
            this.$config.server_url + "/" + route + "/1.0/participations",
            this.row_pa
          );
        }
      }
      if (response.data.err) {
        this.$store.dispatch("showDialogError", response.data.err.message);
        return;
      }
      if (this.from === "backoffice") {
        this.$store.dispatch("showToast", {
          title: "Candidat",
          text: "Le candidat a bien été enregistré",
          color: "green",
        });
      }
      this.row_pa = this.signup
        ? response.data.participation
        : response.data.data;
      this.$emit("formCandidatJureActions", {
        action: "saved",
        row_pa: this.row_pa,
        row_lo: this.signup ? response.data.login : {},
      });
    },
    /*     async deleteCandidat() {
      let route = this.from === "candidats" ? "candidats" : "backoffice";
      let response = await this.$axios.delete(
        this.$config.server_url + "/"+route+"/1.0/participations/" + this.row_pa.pa_id
      );
      this.confirmdelete = false;
      if (response.data.data.success) {
        this.$router.push("/candidats"),
          this.$store.dispatch("showToast", {
            title: "Candidat",
            text:
              "Le candidat " +
              response.data.data.pa_society +
              " a bien été supprimé",
            color: "green"
          });
        this.$emit("formCandidatJureActions", { action: "deleted" });
      }
    } */
  },
};
</script>

<style lang="scss" scoped>
@media (min-width: 768px) {
  .width150 {
    width: 150px;
  }

  .width250 {
    width: 250px;
  }

  .width200 {
    width: 200px;
  }

  .width300 {
    width: 300px;
  }

  .width360 {
    width: 360px;
  }
}
</style>
