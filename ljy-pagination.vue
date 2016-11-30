<!-- 分页的组件，默认第一页从1开始,后端返回成功后才高亮新的页码，这里只发送换页事件，并接受新的当前页和总页数 -->
<template>
	<section>
		<ul class="clearfix">
			<li :class="{'disabled': 1===current}" v-touch:tap="prev">上一页</li>
			<li v-for="index in pageShown" :class="{'active': index===current}" v-touch:tap="changePage(index)">{{index}}</li>
			<li :class="{'disabled': total===current}" v-touch:tap="next">下一页</li>
		</ul>
		<div>共{{total}}页</div>
	</section>
</template>
<style lang="sass" scoped>
	section {
		text-align: center;
		ul>li {
			display: inline-block; padding: 2px 4px; border: 1px solid #000; margin: 6px; curson: pointer;
			&.active {color: red;}
			&.disabled {color: grey;}
		}
	}
</style>
<script>
	/*
	 *total 总页码数
	 *current 当前页码
	 *between 当前页前后最多留的页码数
	 */
	export default {
		props: ['total', 'current', 'between'],
		data(){
			return {
				pageShown: []// 当前显示的页码
			}
		},
		watch: {
			current: function(val, oldVal){
				// 清空页码列表				
				this.pageShown = []
				// upLimit当前列表最大页，bottomLimit当前列表最小页
				// 下面的注释以between为2为例
				if(val <= this.between + 1){
					// 从1到3或总页数在3页以内都进入本逻辑，这种情况总是显示第一页
					// 避免超过总页数
					let upLimit = (val + this.between) > this.total ? this.total : (val + this.between)
					// 避免总数浪费，比如显示1 2 3，其实可以显示到1 2 3 4 5，但是总数不能超过总页数
					if((upLimit - 1) < this.between*2){
						upLimit = (1 + this.between*2) > this.total ? this.total : (1 + this.between*2)
					}

					for (let i = 1; i <= upLimit; i++) {
						this.pageShown.push(i)
					};
				}else if((val + this.between) >= this.total){
					// 总页数超过3页且当前页大于3，且当前页很接近最后一页的进入本逻辑，这种情况总是显示最后一页
					let bottomLimit = val - this.between
					// 避免总数浪费，比如共6页，当前在第6页，那就从第2页开始
					if((this.total - bottomLimit) < this.between*2){
						bottomLimit = (this.total - this.between*2) > 1 ? (this.total - this.between*2) : 1
					}
					for (let i = bottomLimit; i <= this.total; i++) {
						this.pageShown.push(i)
					};
				}else{
					// 当前页在总页数中间，离第一页和最后一页都远（超过between）的进入本逻辑：前后各2页
					let bottomLimit = (val - this.between) 
					let upLimit = (val + this.between)
					for (let i = bottomLimit; i <= upLimit; i++) {
						this.pageShown.push(i)
					};
				}
			}
		},
		methods: {
			changePage(index){
				this.$dispatch('change-page',{
					page: index
				})
			},
			prev(){
				if(this.current === 1){
					return;
				}
				this.changePage(this.current-1)
			},
			next(){
				if(this.current === this.total){
					return;
				}
				this.changePage(this.current+1)
			}
		}
	}
</script>