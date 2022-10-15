<template>
	<div class="live-chat-container">
		<div class="header">
			<div class="icon">
				<img src="/static/angles_double_right.png" />
			</div>
			<div class="title">Lives Chat</div>
		</div>
		<div class="highlight">
			<div class="highlight-total">
				<img src="/static/yellow_gem.png" />
				<div class="gem_amount">
					<div>3,500 gems</div>
					<div>User696969</div>
				</div>
			</div>
			<div class="btn">Buy Gems</div>
		</div>
		<div v-for="item in users" :key="item.id" class="comment-section">
			<img :src="item.profile" alt="profile" class="profile" />
			<div class="comment">
				<div class="title">{{item.name}}</div>
				<div class="sub-comment">{{item.comment}}</div>
			</div>
		</div>
		<div class="comment-footer">
			<div class="comment-container">
				<img src="/static/profile.png" />
				<input v-model="text" placeholder="Show your support..." />
				<img src="/static/emoji_icon.png" />
			</div>
			<div class="action">
				<div class="gem">
					<img src="/static/yellow_gem.png" />
					<div>390 gems</div>
				</div>
				<div class="btn-send">Send</div>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		name:"pc-comment-sidebar",
		data() {
			return {
				users: [
					{
						id: 1,
						name: 'clara',
						comment: 'Sandwich me mommy!!',
						profile: '../../static/profile.png'
					},
					{
						id: 2,
						name: 'clara',
						comment: 'Sandwich me mommy!!',
						profile: '../../static/profile.png'
					},
					{
						id: 3,
						name: 'clara',
						comment: 'Sandwich me mommy!!',
						profile: '../../static/profile.png'
					},
					{
						id: 4,
						name: 'clara',
						comment: 'Sandwich me mommy!!',
						profile: '../../static/profile.png'
					}
				]
			};
		},
	}
</script>

<style>
	.live-chat-container {
		position: relative;
		max-width: 360px;
		height: 80vh;
		width: 100%;
		background: white;
		padding-bottom: 1em;
	}
	.header {
		display: flex;
		width: 100%;
		position: relative;
		justify-content: center;
		border-bottom: 2px solid whitesmoke;
		padding: 1em 0;
	}
	.header .title {
		font-weight: 700;
		font-size: 24px;
	}
	.icon {
		position: absolute;
		top: 50%;
		left: 1em;
		transform: translateY(-50%);
	}
	.icon img {
		max-width: 25px;
		height: 25px;
	}
	.profile, .comment-container img {
		width: 50px;
		height: 50px;
		border-radius: 50%;
	}
	.comment-section {
		display: flex;
		margin-top: 1em;
		padding: 0 1em;
	}
	.comment {
		padding: 0 0.5em;
	}
	.comment .title {
		font-weight: 700;
	}
	.highlight {
		display: flex;
		justify-content: space-between;
		align-items: center;
		background: #9672FB;
	}
	.highlight-total {
		display: flex; 
		width: 50%;
		color: white;
	}
	.highlight-total img {
		float: left;
	}
	.gem_amount {
		display: flex;
		flex-direction: column;
		justify-content: center;
	}
	.gem_amount div:last-child {
		font-size: 11px;
	}
	.btn {
		background: white;
		padding: 0.5em;
		border-radius: 0.2em;
		margin-right: 1em;
		color: purple;
	}
	.comment-container {
		display: flex;
		align-items: center;
		background: #F1F1F1;
		padding: 0.5em;
		margin: 1em;
		border-radius: 0.3em;
	}
	.comment-container input {
		width: 100%;
		margin-left: 0.5em;
	}
	
	.comment-container img {
		max-width: 21px;
		max-height: 21px;
	}
	.action {
		display: flex;
		justify-content: space-between;
		align-items: end;
	}
	.gem {
		display: flex;
		align-items: center;
	}
	.btn-send {
		background: #9672FB;
		padding: 0.5em;
		border-radius: 0.2em;
		margin-right: 1em;
		color: white;
	}
	.gem img {
		width: 30px;
		height: 30px;
		padding-left: 0.5em;
	}
	.comment-footer {
		position: absolute;
		bottom: 0.5em;
		width: 100%;
	}
</style>