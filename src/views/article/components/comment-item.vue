<template>
  <van-cell class="comment-item">
    <van-image
      slot="icon"
      class="avatar"
      round
      fit="cover"
      :src="comment.aut_photo"
    />
    <div slot="title" class="title-wrap">
      <div class="user-name">{{ comment.aut_name }}</div>
      <van-button
        class="like-btn"
        :class="{ liked: comment.is_liking }"
        :icon="comment.is_liking ? 'good-job' : 'good-job-o'"
        :loading="commentLoading"
        @click="onCommentLike"
        >{{ comment.like_count || "赞" }}</van-button
      >
    </div>

    <div slot="label">
      <p class="comment-content">{{ comment.content }}</p>
      <div class="bottom-info">
        <span class="comment-pubdate">{{
          comment.pubdate | relativeTime
        }}</span>
        <van-button
        class="reply-btn"
         round
          @click="$emit('reply-click', comment)"
          >
          回复 {{ comment.reply_count }}</van-button
        >
      </div>
    </div>
  </van-cell>
</template>
<script>
import { addCommentLike, deleteCommentLike } from '@/api/comment'
export default {
  name: 'CommentItem',
  data () {
    return {
      commentLoading: false // 是否点赞中状态
    }
  },
  props: {
    // 每行的评论信息
    comment: {
      type: Object,
      required: true
    }
  },
  methods: {
    // 点赞或取消点赞事件
    async onCommentLike () {
      // loading 开启
      this.commentLoading = true
      try {
        // 如果已经赞了则取消点赞
        if (this.comment.is_liking) {
          await deleteCommentLike(this.comment.com_id)
          this.comment.like_count--
        } else {
          // 如果没有赞，则点赞
          await addCommentLike(this.comment.com_id)
          this.comment.like_count++
        }
        // 更新视图状态
        this.comment.is_liking = !this.comment.is_liking
        this.$toast('操作成功')
      } catch (err) {
        this.$toast('操作失败，请重试')
        console.log(err)
      }
      // loading 关闭
      this.commentLoading = false
    }
  }
}
</script>

<style scoped lang="less">
.comment-item {
  .avatar {
    width: 72px;
    height: 72px;
    margin-right: 25px;
  }
  .title-wrap {
    display: flex;
    justify-content: space-between;
    align-items: center;
    .user-name {
      color: #406599;
      font-size: 26px;
    }
  }
  .comment-content {
    font-size: 32px;
    color: #222222;
    word-break: break-all;
    text-align: justify;
  }
  .comment-pubdate {
    font-size: 19px;
    color: #222;
    margin-right: 25px;
  }
  .bottom-info {
    display: flex;
    align-items: center;
  }
  .reply-btn {
    width: 135px;
    height: 48px;
    line-height: 48px;
    font-size: 21px;
    color: #222;
  }
  .like-btn {
    height: 30px;
    padding: 0;
    border: none;
    font-size: 19px;
    line-height: 30px;
    margin-right: 7px;
    .van-icon {
      font-size: 30px;
    }
  }
  .liked {
    color: orange;
  }
}
</style>
