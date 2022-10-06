<script>
	import Sortable from 'sortablejs';
	import {onMount} from 'svelte'
	import { cards } from '~/store/list'
	import CreateCard from '~/components/CreateCard.svelte'
	import ListTitle from '~/components/ListTitle.svelte'
	import Card from '~/components/Card.svelte'

	export let sortableLists
	export let list 

	let cardsEl
	let sortableCards

	function toggleSortable(event) {
		sortableLists.option('disabled',event.detail)
		sortableCards.option('disabled',event.detail)
	}

	onMount(()=>{
		sortableCards = new Sortable(cardsEl, {
			group: 'my cards',
			handle: '.card',
			delay:50,
			animation:0,
			forceFallback:true,
			onEnd(event){
				console.log(event)
				cards.reorder({
					fromListId:event.from.dataset.listId,
					toListId:event.to.dataset.listId,
					oldIndex:event.oldIndex,
					newIndex:event.newIndex
				})
			}


		})
	})
</script>

<div class="list sortable-ghost">
<div class="list__inner">
	<div class="list__heading">
		<ListTitle 
		{list} 
		on:editMode={toggleSortable}/>
		<p>
			{list.cards.length} cards
		</p>
	</div>
	<div 
		data-list-id={list.id}
		bind:this={cardsEl}
		class="list__cards">
		{#each list.cards as card (card.id)}
		<Card 
		 listId={list.id}
		 {card}
		 on:editMode={toggleSortable}/>
		{/each}
		<CreateCard 
		listId={list.id}
		on:editMode={toggleSortable}/>
	</div>
</div>
</div>


<style lang="scss">
	.list {
		word-break: break-all;
	  // font 관련 속성들은 상속(inherit)되기 때문에,
	  // .list의 자식 요소들에 영향을 주지 않기 위해,
	  // white-space 속성 값을 여기서 초기화합니다.
	  white-space: normal;
	  // inline-block 요소의 띄어쓰기 공간을 부모 요소에서 초기화했기 때문에,
	  // 다시 글자 크기를 원상태로 복원합니다.
	  font-size: 16px;
	  word-break: break-all;
	  display: inline-block;
	  vertical-align: top;
	  line-height: 20px;
	  width: 290px;
	  height: 100%;
	  margin: 0 4px;
	  user-select: none;
	  // .sortable-ghost는 SortableJS에서 생성하는 선택자입니다.
	  // Svelte에서는 Hash가 자동으로 붙기 때문에 :global()를 사용해,
	  // .sortable-ghost 선택자에 Hash가 붙지 않도록 합니다.
	  :global(&.sortable-ghost) {
		opacity: 0.2;
		position: relative;
		&::after {
		  content: "";
		  position: absolute;
		  top: 0;
		  left: 0;
		  width: 100%;
		  height: 100%;
		  background: #000;
		  border-radius: 4px;
		}
	  }
	  // .sortable-chosen는 SortableJS에서 생성하는 선택자입니다.
	  // Svelte에서는 Hash가 자동으로 붙기 때문에 :global()를 사용해,
	  // .sortable-chosen 선택자에 Hash가 붙지 않도록 합니다.
	  :global(&.sortable-chosen) {
		cursor: move;
	  }
	  .list__inner {
		// 카드 목록 영역 크기가 자동 조절이 되도록,
		// Flexible box로 만들고 수직 정렬합니다.
		// 기본 값은 수평 정렬(row)입니다.
		display: flex;
		flex-direction: column;
		max-height: 100%;
		box-sizing: border-box;
		padding: 10px 8px;
		background: #ebecf0;
		border-radius: 4px;
		.list__heading {
		  margin-bottom: 10px;
		  p {
			color: #5E6C84;
			padding: 0 8px;
		  }
		}
		.list__cards {
		  overflow-x: hidden;
		  overflow-y: auto;
		  margin-bottom: 10px;
		}
	  }
	}
  </style>

<!-- <style lang="scss">
	.list {
        display: inline-block;
		width: 290px;
		height: 100%;
		box-sizing: border-box;
		font-size: 16px;
        white-space: normal;
        margin: 0 4px;
		line-height: 20px;
		
		.list__inner{
			display: flex;
			flex-direction: column;
			max-height:100%;
			padding: 10px 8px;
			box-sizing:border-box;
			background: #ebecf0;
			border-radius: 4px;
		}
		.list__heading{
			margin-bottom: 10px;
			p {
				color:#5e6c84;
				padding: 0 8px;
			}
		}
		.list__cards{
			overflow-x: hidden;
			overflow-x: auto;
			margin-bottom: 10px;
		}
    }

	// .sortable-ghost {
    //   	opacity: .2;
    //   	position: relative;
    //   	&::after {
    //     content: "";
    //     position: absolute;
    //     top: 0;
    //     left: 0;
    //     width: 100%;
    //     height: 100%;
    //     background: #000;
    //     border-radius: 4px;
    //   }

	//   :global(&.sortable-chosen){
	// 	cursor: move;
	//   }
	// }
</style> -->
