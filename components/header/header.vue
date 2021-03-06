<template>
	<div>

		<navigation-drawer-component
      		:drawer="drawer"
      		@updateNavigationDrawerModel="updateNavigationDrawerModel"
      		@closeNavigationDrawer="closeNavigationDrawer"
    	/>

		<no-ssr>
			<navigation-drawer-example-profile-component
        		:drawer-example-profile="drawerExampleProfile"
        		@switchNavigationDrawerExampleProfile="switchNavigationDrawerExampleProfile"
        		@updateNavigationDrawerValueExampleProfile="updateNavigationDrawerValueExampleProfile"
        		@closeNavigationDrawerExampleProfile="closeNavigationDrawerExampleProfile"
      		/>
		</no-ssr>

		<toolbar-header-component
      		:drawer="drawer"
      		@switchNavigationDrawer="switchNavigationDrawer"
      		@switchNavigationDrawerExampleProfile="switchNavigationDrawerExampleProfile"
      		@closeNavigationDrawer="closeNavigationDrawer"
   	 	/>

		<toolbar-search-header-component
      		:drawer="drawer"
      		:query-model="queryModel"
      		:query-items="queryItems"
      		:query-input-sync="queryInputSync"
      		:city-model="cityModel"
      		:city-items="cityItems"
      		:city-input-sync="cityInputSync"
      		@updateQueryModel="updateQueryModel"
      		@updateQueryInputSync="updateQueryInputSync"
      		@updateCityModel="updateCityModel"
      		@updateCityInputSync="updateCityInputSync"
      		@submitSearchForm="submitSearchForm"
      		@updateNavigationDrawerModel="updateNavigationDrawerModel"
      		@updateSearchDialogXsModel="updateSearchDialogXsModel"
    	/>

		<no-ssr>
      		<search-dialog-sm-component
        		:search-dialog-sm-model="searchDialogXsModel"
        		:categories-model="categoriesModel"
        		:categories-items="categoriesItems"
        		:query-model="queryModel"
        		:query-items="queryItems"
        		:query-input-sync="queryInputSync"
        		:city-model="cityModel"
        		:city-items="cityItems"
        		:city-input-sync="cityInputSync"
        		@updateCategoriesModel="updateCategoriesModel"
       			@updateQueryModel="updateQueryModel"
        		@updateQueryInputSync="updateQueryInputSync"
        		@updateCityModel="updateCityModel"
        		@updateCityInputSync="updateCityInputSync"
        		@submitSearchForm="submitSearchForm"
        		@updateSearchDialogSmModel="updateSearchDialogSmModel"
      		/>
    	</no-ssr>

		<no-ssr>
			<search-dialog-xs-component
				:search-dialog-xs-model="searchDialogXsModel"
        		:categories-model="categoriesModel"
        		:categories-items="categoriesItems"
        		:query-model="queryModel"
        		:query-items="queryItems"
        		:query-input-sync="queryInputSync"
        		:city-model="cityModel"
        		:city-items="cityItems"
        		:city-input-sync="cityInputSync"
        		@updateCategoriesModel="updateCategoriesModel"
        		@updateQueryModel="updateQueryModel"
        		@updateQueryInputSync="updateQueryInputSync"
        		@updateCityModel="updateCityModel"
        		@updateCityInputSync="updateCityInputSync"
        		@submitSearchForm="submitSearchForm"
        		@updateSearchDialogXsModel="updateSearchDialogXsModel"
			/>
		</no-ssr>

	</div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
import NavigationDrawerComponent from './navigation-drawer.vue';
import NavigationDrawerExampleProfileComponent from './navigation-drawer-example-profile.vue';
import ToolbarHeaderComponent from './toolbar-header.vue';
import ToolbarSearchHeaderComponent from './toolbar-search-header.vue';
import SearchDialogSmComponent from './toolbar-search-header-sm.vue';
import SearchDialogXsComponent from '../profiles-search/search-dialog-xs.vue';
import categoriesItems from '../../json/categories-menu-items.json';

export default {
	components: {
		'navigation-drawer-component': NavigationDrawerComponent,
    	'navigation-drawer-example-profile-component': NavigationDrawerExampleProfileComponent,
    	'toolbar-header-component': ToolbarHeaderComponent,
    	'toolbar-search-header-component': ToolbarSearchHeaderComponent,
    	'search-dialog-sm-component': SearchDialogSmComponent,
    	'search-dialog-xs-component': SearchDialogXsComponent,
	},
	data() {
		return {
			drawer: false,
      		drawerExampleProfile: false,
      		cityInputSync: null,
      		queryInputSync: null,
      		searchDialogSmModel: true,
      		searchDialogXsModel: false,
      		categoriesModel: null,
      		categoriesItems: categoriesItems.categories,
		}
	},
	computed: {
		...mapGetters('searchProfiles', [
      		'query',
      		'city',
    	]),
    	...mapGetters('autocomplete', [
      		'queryItems',
      		'cityItems',
    	]),
    	queryModel: {
      		get() {
        		return this.$store.state.searchProfiles.query;
      		},
      		set(value) {
        		this.$store.dispatch('searchProfiles/queryUpdate', value);
      		},
    	},
    	cityModel: {
      		get() {
        		return this.$store.state.searchProfiles.city;
      		},
      		set(value) {
        		this.$store.dispatch('searchProfiles/cityUpdate', value);
      		},
    	},
	},
	watch: {
		cityInputSync(value) {
    		if (value) {
    			this.$store.dispatch('autocomplete/autocompleteCity', value);
    		}

    		this.cityModel = this.checkIsNullWatch(value);
    	},
    	queryInputSync(value) {
    		if (value) {
    			this.$store.dispatch('autocomplete/autocompleteQuery', value);
    		}

    		this.queryModel = this.checkIsNullWatch(value);
    	},
	},
	methods: {
		...mapActions('searchProfiles', [
    		'getProfilesData',
    	  	'queryUpdate',
    	  	'cityUpdate',
    	]),
    	...mapActions('autocomplete', [
    		'autocompleteQuery',
    		'autocompleteCity',
		]),
		submitSearchForm() {
      		const parameters = {};

      		window.scrollTo(0, 0);

      		parameters.query = this.checkIsNull(this.query);
      		parameters.city = this.checkIsNull(this.city);

      		document.title = this.documentTitle(parameters.query, parameters.city);

      		this.updateURL(`${this.checkLanguage()}/search?query=${parameters.query}&city=${parameters.city}`);
      		this.$store.dispatch('searchProfiles/getProfilesData', parameters);
		},
		documentTitle(query, city) {
			let queryText = '';
      		let cityText = '';

      		if (query !== '') queryText = ` - ${query}`;
      		if (city !== '') cityText = ` - ${city}`;

      		return `Yourcity ${queryText} ${cityText}`;
		},
		updateURL(url) {
      		const stateObj = { foo: 'bar' };
      		history.pushState(stateObj, 'page', url); // eslint-disable-line
		},
		checkLanguage(url) {
			return this.$i18n.locale !== 'en' ? `/${this.$i18n.locale}` : '';
		},
		checkIsNull(value) {
			return value == null ? value : '';
		},
		checkIsNullWatch(value) {
			return value != null ? value : '';
		},
		switchNavigationDrawer() {
      		this.drawer = !this.drawer;
    	},
    	updateNavigationDrawerModel(value) {
    		this.drawer = value;
    	},
    	closeNavigationDrawer() {
    		this.drawer = false;
    	},
    	switchNavigationDrawerExampleProfile() {
    		this.drawerExampleProfile = !this.drawerExampleProfile;
    	},
    	closeNavigationDrawerExampleProfile() {
    		this.drawerExampleProfile = false;
    	},
    	showNavigationDrawerExampleProfile() {
    		this.drawerExampleProfile = true;
    	},
    	updateNavigationDrawerValueExampleProfile(value) {
    		this.drawerExampleProfile = value;
    	},
    	updateQueryModel(value) {
    		this.queryModel = value;
    	},
    	updateQueryInputSync(value) {
    		this.queryInputSync = value;
    	},
    	updateCityModel(value) {
    		this.cityModel = value;
    	},
    	updateCityInputSync(value) {
    		this.cityInputSync = value;
    	},
    	updateCategoriesModel(value) {
    		this.categoriesModel = value;
    	},
    	updateSearchDialogSmModel(value) {
    		this.searchDialogXsModel = value;
    	},
    	updateSearchDialogXsModel(value) {
    		this.searchDialogXsModel = value;
    	},
	}
}
</script>
