<script>
	import { Masterchat, stringify } from "masterchat";
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

//   import translate from "translate";
  let open = false;
  const toggle = () => (open = !open);
  let url = ""
  function gogetlive() {
	console.log(url);
	open = !open
	main();
  }
	let comments = [];
	let mambercomments = [];
	let supercomments = [];
	async function main() {
		const mc = await Masterchat.init(url);
		mc.on("chat", (chat) => {
			console.log(chat.authorName, stringify(chat.message));
			console.log(chat);
			comments.push(
				{
					authorName: chat.authorName,
					authorhead: chat.authorPhoto,
					message: stringify(chat.message)
				}
			);
			comments=comments
			if (chat.membership != undefined) {
				mambercomments.push(stringify(chat.message));
				mambercomments=mambercomments
			}

		});
		mc.listen();
	}
</script>

<main>
	<Row>
		<Col xs="1">
			<div class="sidelist">
				<div class="url_button">
				<Button color="danger" on:click={toggle}>Open Modal</Button>
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
			<div class="item">
				{#each comments as comment}
					<article>
						<img src={comment.authorhead} alt="">
						<p>{comment.authorName}</p>
						<p>{comment.message}</p>
					</article>
				{/each}
			</div>
			<div class="item">
				{#each mambercomments as comment}
					<article>
						<p>{comment}</p>
					</article>
				{/each}
			</div>
			<div class="item">
				{#each supercomments as comment}
					<article>
						<p>{comment}</p>
					</article>
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
	

    	

</style>