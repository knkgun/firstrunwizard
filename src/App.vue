<template>
	<Modal
		v-if="showModal && slideList.length > 0"
		id="firstrunwizard"
		:has-previous="hasPrevious"
		:has-next="hasNext"
		:size="isMobile ? 'full' : 'normal'"
		:clear-view-delay="-1 /* disable fade-out because of accessibility reasons */"
		name="modal"
		@previous="previous"
		@next="next"
		@close="close">
		<div v-if="currentSlide !== 0 || !withIntro" class="modal-header">
			<div class="firstrunwizard-header">
				<div class="logo">
					<p class="hidden-visually">
						{{ oc_defaults.name }}
					</p>
				</div>
				<!-- eslint-disable-next-line vue/no-v-html -->
				<h2 v-html="oc_defaults.slogan" />
				<p />
			</div>
		</div>
		<div class="modal-body">
			<slot v-if="slideList.length > 0" name="body">
				<transition :name="fadeDirection" mode="out-in">
					<!-- eslint-disable-next-line vue/no-v-html -->
					<div v-if="slideList[currentSlide].type === 'inline'" :key="currentSlide" v-html="slideList[currentSlide].content" />
					<div :is="slideList[currentSlide]" v-else @finished="currentSlide++" />
				</transition>
			</slot>
		</div>
		<div class="modal-footer">
			<button v-if="isLast" class="primary modal-default-button" @click="close">
				{{ startButtonText }}
			</button>
		</div>
	</Modal>
</template>
<style lang="scss">
	/* Page styling needs to be unscoped, since we load it separately from the server */
	#firstrunwizard {

		.page {
			display: flex;
			flex-direction: row;
			flex-wrap: wrap;
			margin: auto;

			h3 {
				margin: 10px 0 10px;
				line-height: 120%;
				padding: 0;
			}
			.image {
				padding: 20px;
				max-width: calc(50% - 40px);
				flex-grow: 1;
				img {
					width: 100%;
				}
			}
			.content {
				padding: 20px;
				width: 100%;
			}
			p {
				margin-bottom: 20px;
			}
			.description-block:first-child {
				margin-bottom: 20px;
			}
			.description {
				margin: 20px;
				width: auto;
				flex-grow: 1;
				max-width: calc(50% - 40px);
			}
			ul {
				margin: 10px;
				li {
					margin-left: 20px;
					margin-bottom: 10px;
					list-style: circle outside;
				}
			}
			a:not(.button) {
				&:hover,
				&:focus {
					text-decoration: underline;
				}
			}
			.button {
				display: inline-block;

				img {
					width: 16px;
					height: 16px;
					opacity: .5;
					margin-top: -3px;
					vertical-align: middle;
				}
			}
		}

		.content-clients {
			width: 100%;
			text-align: center;
			a {
				text-decoration: none;
				display: inline-block;
			}
			.clientslinks .appsmall {
				height: 32px;
				width: 32px;
				position: relative;
				opacity: .5;
				vertical-align: middle;
			}
			.clientslinks .button {
				display: inline-block;
				padding: 8px;
				font-weight: normal;
				font-size: 14px;
			}
		}
		.content-final {
			h3 {
				background-position: 0;
				background-size: 16px 16px;
				padding-left: 26px;
				opacity: .7;
			}
		}
		p a {
			font-weight: bold;
			color: var(--color-primary);
			&:hover,
			&:focus {
				color: var(color-text-light);
			}
		}

		.footnote {
			margin-top: 40px;
		}

		// primary on next button
		.modal-wrapper .icon-next {
			background-color: var(--color-primary);
			color: var(--color-primary-text);
			box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
			left: 22px;
		}
	}

	.clientslinks {
		margin-top: 20px;
		margin-bottom: 20px;
	}

	#wizard-values {
		list-style-type: none;
		display: flex;
		flex-wrap: wrap;
		margin: 0;
		li {
			display: block;
			min-width: 250px;
			width: 33%;
			flex-grow: 1;
			margin: 20px 0 20px 0;
			span {
				opacity: .7;
				display: block;
				height: 50px;
				width: 50px;
				background-size: 40px;
				margin: auto;
			}
			h3 {
				margin: 10px 0 10px 0;
				font-size: 130%;
				text-align: center;
			}
		}
	}

	.details-link {
		text-align: center;
	}

	@media only screen and (max-width: 680px) {
		#firstrunwizard {
			.firstrunwizard-header div.logo {
				background-size: 120px;
			}
			h2 {
				font-size: 20px;
			}
			.page > div {
				max-width: 100% !important;
				width: 100%;
			}
			.page {
				#wizard-values li {
					min-width: 100%;
					overflow: hidden;
					display: flex;
					span {
						width: 44px !important;
						padding-right: 20px;
						flex-grow: 0;
					}
					h3 {
						font-size: 12px;
						text-align: left;
						flex-grow: 1;
					}
				}

				&.content-final {
					padding-bottom: 50px;
				}
			}
		}
	}
</style>

<style lang="scss" scoped>
	.modal-mask {
		background-color: rgba(0, 0, 0, 0.7);

		&::v-deep .modal-wrapper {
			position: relative;
		}

		&::v-deep .modal-container {
			display: flex;
			flex-direction: column;
			height: 95% !important;
			width: 95% !important;
			max-width: 900px;
			max-height: 650px !important;
			position: relative;
		}

		.modal-body {
			flex-grow: 1;
			display: flex;
			overflow-x: hidden;
			overflow-y: auto;

			& > div {
				display: flex;
				flex-grow: 1;
				align-items: center;
				justify-content: center;
			}
		}
	}

	.modal-header {
		height: 180px;
		max-height: 40vh;
		overflow: hidden;
		flex-shrink: 0;

		.firstrunwizard-header {
			padding: 20px 12px;
			background: var(--color-primary) var(--image-login-background) no-repeat 50% 50%;
			background-size: cover;
			color: var(--color-primary-text);
			text-align: center;
			.logo {
				background: var(--image-logo) no-repeat center;
				background-size: contain;
				width: 175px;
				height: 100px;
				max-height: 20vh;
				margin: 0 auto;
			}
			h2 {
				font-size: 20px;
				margin-top: 7px;
				line-height: 150%;
				color: var(--color-primary-text);
				font-weight: 300;
				padding: 0 0 10px;
			}
		}
	}

	.modal-default-button {
		align-self: flex-end;
	}

	.modal-footer {
		overflow: hidden;
		position: absolute;
		display: flex;
		bottom: 0;
		right: 0;
	}

	.modal-footer button {
		margin: 10px;
	}

	/* Transitions */
	.next-enter-active, .next-leave-active,
	.previous-enter-active, .previous-leave-active {
		transition: transform .1s, opacity .25s;
	}

	.next-enter {
		transform: translateX(50%);
		opacity: 0;
	}

	.next-leave-to {
		transform: translateX(-50%);
		opacity: 0;
	}

	.previous-enter {
		transform: translateX(-50%);
		opacity: 0;
	}

	.previous-leave-to {
		transform: translateX(50%);
		opacity: 0;
	}
</style>
<script>
import Modal from '@nextcloud/vue/dist/Components/Modal'
import axios from '@nextcloud/axios'
import { generateUrl } from '@nextcloud/router'
import IntroVideo from './components/IntroVideo'

export default {
	name: 'App',
	components: {
		Modal,
	},
	data() {
		return {
			showModal: false,
			withIntro: true,
			slides: [],
			currentSlide: 0,
			fadeDirection: 'next',
			isMobile: window.outerWidth < 1024,
			slidesLoaded: false,
		}
	},
	computed: {
		slideList() {
			if (this.withIntro) {
				return this.slides
			}
			const slides = this.slides
			return slides.slice(1)
		},
		hasNext() {
			return this.currentSlide < this.slideList.length - 1
		},
		hasPrevious() {
			return this.currentSlide > 0
		},
		isLast() {
			return this.currentSlide === this.slideList.length - 1
		},
		isFirst() {
			return this.currentSlide === 0
		},
		startButtonText() {
			return t('firstrunwizard', 'Start using {cloudName}', { cloudName: window.OC.theme.name })
		},
	},
	async created() {
		this.slides = [IntroVideo]
		window.addEventListener('resize', this.onResize)
	},
	beforeDestroy() {
		window.removeEventListener('resize', this.onResize)
	},
	methods: {
		async loadStaticSlides() {
			if (this.slidesLoaded) {
				return
			}

			try {
				const response = await axios.get(generateUrl('/apps/firstrunwizard/wizard'))
				this.slides.push(...response.data.slides)
				this.withIntro = response.data.hasVideo
				this.slidesLoaded = true
			} catch (e) {
				console.error('Failed to load slides')
			}
		},
		async open(withIntro = true) {
			await this.loadStaticSlides()
			this.withIntro = this.withIntro & withIntro
			this.showModal = true
			this.currentSlide = 0
		},
		close() {
			this.showModal = false
			axios.delete(generateUrl('/apps/firstrunwizard/wizard'))
		},
		next() {
			this.fadeDirection = 'next'
			if (this.isLast) {
				this.close()
				return
			}
			this.currentSlide += 1
		},
		previous() {
			this.fadeDirection = 'previous'
			if (this.isFirst) {
				return
			}
			this.currentSlide -= 1
		},
		onResize(event) {
			// Update mobile mode
			this.isMobile = window.outerWidth < 768
		},
	},
}
</script>
