<script>
	import { Masterchat, stringify } from "masterchat";
	import { beforeUpdate, afterUpdate } from 'svelte';
	import {
		Button,
		Modal,
		ModalBody,
		ModalFooter,
		ModalHeader,
		Input,
		FormGroup,
		Row,
		Col
	} from 'sveltestrap';
	import {
		Card,
		CardBody,
		CardFooter,
		CardHeader,
		CardSubtitle,
		CardText,
		CardTitle
	} from 'sveltestrap';

//   import translate from "translate";
	let open = false;
	const toggle = () => (open = !open);
	let url = ""
	let comments = [];
	let mambercomments = [];
	let supercomments = [];
	let flag = false;
	let going = false;

	function gogetlive() {
		if(going == false) {
			open = !open
			main();
			going = true;
		}else{
			flag = true;
			going = false;
			gogetlive();
		}


	}

	async function main() {
		const mc = await Masterchat.init(url);
		if (flag) {
            mc.stop()
        }
		mc.on("chat", (chat) => {
			comments.push(
				{
					authorName: chat.authorName,
					authorhead: chat.authorPhoto,
					message:chat.message,
					ismember: chat.membership
				}
			);
			comments=comments
			if (chat.membership != undefined) {
				mambercomments.push(
					{
						authorName: chat.authorName,
						authorhead: chat.authorPhoto,
						message: chat.message,
						ismember: chat.membership
					}
				);
				mambercomments=mambercomments
			}

		});
		mc.on("actions", (actions) => {
			const superChats = actions.filter(
				(action) => action.type === "addSuperChatItemAction"
			);
			const JoinMember = actions.filter(
				(action) => action.type === "addMembershipItemAction"
			);
			const MemberChat = actions.filter(
				(action) => action.type === "addMembershipMilestoneItemAction"
			);

			for (const sc of superChats) {
				const label = `SC ${sc.amount} ${sc.currency}`;
				console.log(`[${label}] ${sc.authorName}: ${stringify(sc.message)}`);
				console.log(sc);
				switch(sc.color){
					case "lightblue":
						sc.color = "info"
					case "red":
						sc.color = "danger"
					case "yellow":
						sc.color = "warning"
					default:
						sc.color = "dark"
						
				}
				supercomments.push(
					{
						authorName: sc.authorName,
						authorhead: sc.authorPhoto,
						message: sc.message,
						type : sc.type,
						superchat: sc.superchat,
						color: sc.color
					}
				);
				supercomments=supercomments
			}
			for (const jm of JoinMember) {
				console.log(jm);
				supercomments.push(
					{
						authorName: jm.authorName,
						authorhead: jm.authorPhoto,
						type : jm.type,
						membershipphoto: jm.membership.thumbnail,
						joinlevel: jm.level
					}
				);
				supercomments=supercomments
			}
			for (const mc of MemberChat) {
				console.log(mc);
				supercomments.push(
					{
						authorName: mc.authorName,
						authorhead: mc.authorPhoto,
						message: mc.message,
						type : mc.type,
						membership: mc.membership
					}
				);
				supercomments=supercomments
			}
		});
		
		mc.listen();
	}



	let div1,div2,div3;
	let autoscroll1,autoscroll2,autoscroll3;
	// 如果在最下面
	beforeUpdate(() => {
		autoscroll1 = div1 && (div1.offsetHeight + div1.scrollTop) > (div1.scrollHeight - 40);
		autoscroll2 = div2 && (div2.offsetHeight + div2.scrollTop) > (div2.scrollHeight - 40);
		autoscroll3 = div3 && (div3.offsetHeight + div3.scrollTop) > (div3.scrollHeight - 40);

	});
	// 滑動到最下面
	afterUpdate(() => {
		if (autoscroll1) div1.scrollTo(0, div1.scrollHeight);
		if (autoscroll2) div2.scrollTo(0, div2.scrollHeight);
		if (autoscroll3) div3.scrollTo(0, div3.scrollHeight);
	});
</script>

<main>
	<Row>
		<Col xs="1">
			<div class="sidelist">
				<div class="url_button">
				<Button color="danger" on:click={toggle}>enter url</Button>
				<Modal isOpen={open} {toggle}>
					<ModalHeader {toggle}>填入直播網址</ModalHeader>
					<FormGroup>
					<Input
					type="url"
					name="url"
					id="url"
					placeholder="url here"
					bind:value={url}
					/>
					</FormGroup>
					<ModalFooter>
					<Button color="primary" on:click={gogetlive}>確定</Button>
					<Button color="secondary" on:click={toggle}>取消</Button>
					</ModalFooter>
				</Modal>
				</div>
			</div>
		</Col>
		<Col xs="11">
		<div class="contant">
			<div class="item" bind:this={div1}>
				{#each comments as comment}
					{#if comment.ismember == undefined}
					<article>
						<div class="livetalk">
							<img src={comment.authorhead} alt="" style="margin-right:5px">
							<div class="ismemberimg">
								<!-- svelte-ignore a11y-missing-attribute -->
								<span class="notmember">{comment.authorName}</span>
								{#each comment.message as message}
									{#if message.text != undefined}
										<p>{message.text}</p>
									{:else if message.emoji != undefined}
										<img src={message.emoji.image.thumbnails[0].url} alt="">
									{/if}
								{/each}
							</div>
						</div>
					</article>
					{:else}
					<article>
						<div class="livetalk">
							<img src={comment.authorhead} alt="" style="margin-right:5px">
							<div class="ismemberimg">

								<!-- svelte-ignore a11y-missing-attribute -->
								<span class="showmember">{comment.authorName}<img src={comment.ismember.thumbnail}></span>
								{#each comment.message as message}
									{#if message.text != undefined}
										<p>{message.text}</p>
									{:else if message.emoji != undefined}
										<img src={message.emoji.image.thumbnails[0].url} alt="" class="notcircle">
									{/if}
								{/each}
							</div>
						</div>
					</article>
					{/if}
				{/each}
			</div>
			<div class="item" bind:this={div2}>
				{#each mambercomments as comment}
					<article>
						<div class="livetalk">
							<img src={comment.authorhead} alt="" style="margin-right:5px">
							<div class="ismemberimg">

								<!-- svelte-ignore a11y-missing-attribute -->
								<span class="showmember">{comment.authorName}<img src={comment.ismember.thumbnail}></span>
								{#each comment.message as message}
									{#if message.text != undefined}
										<p>{message.text}</p>
									{:else if message.emoji != undefined}
										<img src={message.emoji.image.thumbnails[0].url} alt="" class="notcircle">
									{/if}
								{/each}
							</div>
						</div>
					</article>
				{/each}
			</div>
			<div class="item">
				{#each supercomments as comment}
					{#if comment.type == "addSuperChatItemAction"}
					<article>
						<div class="superchat">
						<Card color={comment.color}>
							<CardHeader style="background-color: rgba({comment.superchat.headerBackgroundColor.r},{comment.superchat.headerBackgroundColor.g},{comment.superchat.headerBackgroundColor.b},1)">
								<span class="schead"><img src={comment.authorhead} alt="" style="margin-right:5px">{comment.authorName}</span>
								<p style="margin-block-end: 0em">{comment.superchat.amount} {comment.superchat.currency}</p>
							</CardHeader>
							<CardBody style="background-color: rgba({comment.superchat.bodyBackgroundColor.r},{comment.superchat.bodyBackgroundColor.g},{comment.superchat.bodyBackgroundColor.b},1)">
								<CardText>
								{#each comment.message as message}
								{#if message.text != undefined}
									<span>{message.text}</span>
								{:else if message.emoji != undefined}
									<img src={message.emoji.image.thumbnails[0].url} alt="" class="notcircle">
								{/if}
								{/each}
							  </CardText>
							</CardBody>
						</Card>
						</div>
					</article>
					{:else if comment.type == "addMembershipItemAction"}
					<article>
						<Card color="success">
							<CardHeader>
								<span class="schead"><img src={comment.authorhead} alt="" style="margin-right:5px">{comment.authorName}<img src={comment.membershipphoto} alt="" style="margin-left:5px"></span>
							</CardHeader>
							<CardBody>
								<CardText>
									Welcome join {comment.joinlevel}
							  </CardText>
							</CardBody>
						  </Card>
					</article>
					{:else if comment.type == "addMembershipMilestoneItemAction"}
					<article>
						<div class="superchat">
						<Card color="success">
							<CardHeader>
								<span class="schead"><img src={comment.authorhead} alt="" style="margin-right:5px"> {comment.authorName} <img src={comment.membership.thumbnail} alt="" class="notcircle" style="margin-left:5px"></span>
								<p style="margin-block-end: 0em">{comment.membership.since}</p>
							</CardHeader>
							<CardBody>
								<CardText>
								{#each comment.message as message}
								{#if message.text != undefined}
									<span>{message.text}</span>
								{:else if message.emoji != undefined}
									<img src={message.emoji.image.thumbnails[0].url} alt="" class="notcircle">
								{/if}
								{/each}
							  </CardText>
							</CardBody>
						</Card>
						</div>
					</article>
					{/if}
				{/each}
			</div>

		</div>
		</Col>
	</Row>
</main>


<style>
	article {
		margin: 0.5em 0;
	}
	main{
		overflow: hidden;
		color: #dfe6e9;
	}
	img{
		max-width: 100%;
		border-radius: 50%;
		height: 30px;
	}
	.notcircle{
		border-radius: 0%;
	}
	.contant {
		display: flex;	
		max-height: 100vh;
		background-color: #2d3436;
	}
	.item {
		flex: 1 1 auto;
		margin: 10px 5px;
		overflow-y: auto;
		overflow-x: hidden;
		width: 500px;
		height: 100vh;
	}
	.sidelist {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}
	.url_button {
		display: flex;
		justify-content: center;
		align-items: center;
		margin: 10px;
	}
	.ismemberimg img{
		height:23px;
	}
	.ismemberimg p{
		display: inline-block;
	}
	.livetalk{
		display: flex;
		overflow-wrap: break-word;
		font-size: 13px;
	}
	.livetalk img{
		height:25px;
	}
	.showmember{
		color: #2ba640;
	}
	.notmember{
		color: rgba(200, 200, 200, 0.5);
	}
	.schead{
		display: flex;
		align-items: center;
	}
	.superchat{
		color: black;
		
	}



</style>