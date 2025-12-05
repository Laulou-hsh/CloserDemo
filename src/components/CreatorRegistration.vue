<template>
  <div class="creator-registration">
    <!-- Header -->
    <header class="header">
      <div class="header-left">
        <h1 class="logo">Clsr</h1>
      </div>
      <div class="header-right">
        <div class="user-info">
          <img src="https://picsum.photos/24/24" alt="User avatar" class="user-avatar" />
          <span class="user-name">Chris Wu</span>
          <span class="user-id">#9733002</span>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="main-content">
      <!-- Sidebar -->
      <aside class="sidebar">
        <nav class="nav-menu">
          <ul>
            <li class="nav-item active">
              <span class="nav-icon">🏠</span>
              <span class="nav-text">Home</span>
            </li>
            <li class="nav-item">
              <span class="nav-icon">📋</span>
              <span class="nav-text">Order</span>
            </li>
            <li class="nav-item">
              <span class="nav-icon">💬</span>
              <span class="nav-text">Message</span>
            </li>
          </ul>
        </nav>
      </aside>

      <!-- Main Content Area -->
      <div class="main-content-area">
        <!-- Breadcrumb -->
        <div class="breadcrumb">
          <span>Home</span> / <span>Manage my page</span> / <span>Become a creator</span>
        </div>

        <!-- Content Area -->
        <div class="content-area">
          <!-- Left Section - Form -->
          <section class="form-section">
            <h2 class="section-title">Become a creator</h2>

            <!-- Step 1: Choose URL (disabled) -->
            <div class="form-step">
              <div class="step-number">1</div>
              <div class="step-content">
                <h3 class="step-title">Choose your URL</h3>
                <div class="url-input">
                  <el-input 
                    placeholder="Closr.io/" 
                    disabled
                    class="disabled-input"
                  />
                </div>
              </div>
            </div>

            <!-- Step 2: Username -->
            <div class="form-step">
              <div class="step-number">2</div>
              <div class="step-content">
                <h3 class="step-title">Username</h3>
                <el-input 
                  v-model="creatorInfo.username" 
                  placeholder="Please Enter" 
                  @input="updatePreview"
                />
              </div>
            </div>

            <!-- Step 3: Profile & Background -->
            <div class="form-step">
              <div class="step-number">3</div>
              <div class="step-content">
                <h3 class="step-title">Profile & Background</h3>
                <div class="upload-container">
                  <div class="upload-item">
                    <el-upload
                      action="#"
                      :show-file-list="false"
                      :before-upload="handleAvatarUpload"
                      :auto-upload="false"
                      accept="image/*"
                    >
                      <div class="upload-box">
                        <img v-if="creatorInfo.avatar" :src="creatorInfo.avatar" class="upload-image" />
                        <div v-else class="upload-placeholder">+</div>
                      </div>
                    </el-upload>
                    <span class="upload-label">Profile Picture</span>
                  </div>
                  <div class="upload-item">
                    <el-upload
                      action="#"
                      :show-file-list="false"
                      :before-upload="handleBackgroundUpload"
                      :auto-upload="false"
                      accept="image/*"
                    >
                      <div class="upload-box background-upload">
                        <img v-if="creatorInfo.background" :src="creatorInfo.background" class="upload-image" />
                        <div v-else class="upload-placeholder">+</div>
                      </div>
                    </el-upload>
                    <span class="upload-label">Profile Background</span>
                  </div>
                </div>
              </div>
            </div>

            <!-- Step 4: About Me -->
            <div class="form-step">
              <div class="step-number">4</div>
              <div class="step-content">
                <h3 class="step-title">About me</h3>
                <el-input
                  v-model="creatorInfo.aboutMe"
                  type="textarea"
                  placeholder="Please Enter"
                  :rows="4"
                  maxlength="500"
                  show-word-limit
                  @input="updatePreview"
                />
              </div>
            </div>

            <!-- Step 5: Social Media -->
            <div class="form-step">
              <div class="step-number">5</div>
              <div class="step-content">
                <h3 class="step-title">Social Media</h3>
                <div class="social-media-icons">
                  <div 
                    v-for="social in socialMediaList" 
                    :key="social.platform"
                    class="social-icon-wrapper"
                    @click="toggleSocialInput(social.platform)"
                  >
                    <el-icon :class="['social-icon', { active: social.isActive }]">
                      <component :is="social.icon" />
                    </el-icon>
                  </div>
                </div>
                <div class="social-media-inputs">
                  <div 
                    v-for="social in socialMediaList" 
                    :key="social.platform"
                    v-show="social.isEditing"
                    class="social-input-item"
                  >
                    <el-icon class="social-input-icon"><component :is="social.icon" /></el-icon>
                    <el-input
                      v-model="social.link"
                      placeholder="Please enter the link"
                      class="social-input"
                    />
                    <el-button 
                      type="primary" 
                      size="small"
                      @click="saveSocialLink(social.platform)"
                      :disabled="!social.link"
                    >
                      Confirm
                    </el-button>
                    <el-button 
                      v-if="social.isActive"
                      size="small"
                      @click="removeSocialLink(social.platform)"
                    >
                      <el-icon><Delete /></el-icon>
                    </el-button>
                  </div>
                </div>
              </div>
            </div>

            <!-- Step 6: Tags (disabled) -->
            <div class="form-step">
              <div class="step-number">6</div>
              <div class="step-content">
                <h3 class="step-title">Tags</h3>
                <div class="tags-container">
                  <el-tag 
                    v-for="tag in availableTags" 
                    :key="tag"
                    :type="getTagType(tag)"
                    effect="plain"
                    size="small"
                  >
                    {{ tag }}
                  </el-tag>
                </div>
              </div>
            </div>

            <!-- Continue Button -->
            <div class="continue-button-container">
              <el-button type="primary" size="large" class="continue-button">Continue</el-button>
            </div>
          </section>

          <!-- Right Section - Preview -->
          <section class="preview-section">
            <div class="preview-card">
              <!-- Background -->
              <div class="preview-background"></div>
              
              <!-- Profile Info -->
              <div class="preview-profile">
                <div class="avatar-container">
                  <el-upload
                    action="#"
                    :show-file-list="false"
                    :before-upload="handleAvatarUpload"
                    :auto-upload="false"
                    accept="image/*"
                  >
                    <img 
                      :src="creatorInfo.avatar || defaultAvatar" 
                      class="preview-avatar"
                      @click="triggerAvatarUpload"
                    />
                  </el-upload>
                  <div class="avatar-edit-icon">
                    <el-icon><Setting /></el-icon>
                  </div>
                </div>
                
                <h2 class="preview-username">{{ creatorInfo.username || 'Please Enter' }}</h2>
                <p class="preview-about">{{ creatorInfo.aboutMe || 'Please Enter' }}</p>
                
                <!-- Social Media Links -->
                <div class="preview-social-links">
                  <template v-for="item in socialMediaList" :key="item.platform">
                    <a 
                      v-if="item.isActive && item.link"
                      :href="item.link"
                      target="_blank"
                      rel="noopener noreferrer"
                      class="preview-social-link"
                    >
                      <el-icon><component :is="item.icon" /></el-icon>
                    </a>
                  </template>
                </div>
              </div>
            </div>
          </section>
        </div>
      </div>
    </main>

    <!-- Hidden file inputs for image uploads -->
    <input 
      ref="avatarInput" 
      type="file" 
      accept="image/*" 
      style="display: none" 
      @change="onAvatarFileChange"
    />
    <input 
      ref="backgroundInput" 
      type="file" 
      accept="image/*" 
      style="display: none" 
      @change="onBackgroundFileChange"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import {
  Link,
  Delete,
  Setting,
  CirclePlus
} from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'

// Types
interface SocialMedia {
  platform: string
  icon: any
  link: string
  isActive: boolean
  isEditing: boolean
}

interface CreatorInfo {
  username: string
  avatar: string
  background: string
  aboutMe: string
}

// Default images
const defaultAvatar = 'https://picsum.photos/100/100'
// const defaultBackground = 'https://picsum.photos/600/200'

// Creator info
const creatorInfo = reactive<CreatorInfo>({
  username: '',
  avatar: '',
  background: '',
  aboutMe: ''
})

// Social media list
const socialMediaList = reactive<SocialMedia[]>([
  { platform: 'instagram', icon: CirclePlus, link: '', isActive: false, isEditing: false },
  { platform: 'tiktok', icon: CirclePlus, link: '', isActive: false, isEditing: false },
  { platform: 'youtube', icon: CirclePlus, link: '', isActive: false, isEditing: false },
  { platform: 'twitter', icon: CirclePlus, link: '', isActive: false, isEditing: false },
  { platform: 'link', icon: Link, link: '', isActive: false, isEditing: false }
])

// Available tags
const availableTags = [
  'Health maintenance',
  'Food travel',
  'Art design',
  'Sport',
  'Travel'
]

// File inputs refs
const avatarInput = ref<HTMLInputElement | null>(null)
const backgroundInput = ref<HTMLInputElement | null>(null)

// Update preview (computed would be better but this is for demo)
const updatePreview = () => {
  // Real-time update happens automatically due to reactivity
}

// Toggle social media input
const toggleSocialInput = (platform: string) => {
  const social = socialMediaList.find(s => s.platform === platform)
  if (social) {
    // Close all other inputs
    socialMediaList.forEach(s => {
      if (s.platform !== platform) {
        s.isEditing = false
      }
    })
    social.isEditing = !social.isEditing
  }
}

// Save social media link
const saveSocialLink = (platform: string) => {
  const social = socialMediaList.find(s => s.platform === platform)
  if (social && social.link) {
    social.isActive = true
    social.isEditing = false
    ElMessage.success('Social link saved successfully')
  }
}

// Remove social media link
const removeSocialLink = (platform: string) => {
  const social = socialMediaList.find(s => s.platform === platform)
  if (social) {
    social.isActive = false
    social.link = ''
    social.isEditing = false
    ElMessage.success('Social link removed successfully')
  }
}

// Handle avatar upload
const handleAvatarUpload = (file: File) => {
  const reader = new FileReader()
  reader.onload = (e) => {
    creatorInfo.avatar = e.target?.result as string
  }
  reader.readAsDataURL(file)
  return false
}

// Handle background upload
const handleBackgroundUpload = (file: File) => {
  const reader = new FileReader()
  reader.onload = (e) => {
    creatorInfo.background = e.target?.result as string
  }
  reader.readAsDataURL(file)
  return false
}

// Trigger avatar upload
const triggerAvatarUpload = () => {
  avatarInput.value?.click()
}

// On avatar file change
const onAvatarFileChange = (event: Event) => {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  if (file) {
    handleAvatarUpload(file)
  }
  // Reset input
  if (target) {
    target.value = ''
  }
}

// On background file change
const onBackgroundFileChange = (event: Event) => {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  if (file) {
    handleBackgroundUpload(file)
  }
  // Reset input
  if (target) {
    target.value = ''
  }
}

// Get tag type
const getTagType = (tag: string) => {
  const tagTypes: Record<string, any> = {
    'Health maintenance': 'success',
    'Art design': 'danger',
    'Sport': 'info'
  }
  return tagTypes[tag] || ''
}
</script>

<style lang="scss">
.creator-registration {
  width: 100%;
  min-height: 100vh;
  background-color: #fff;
  display: flex;
  flex-direction: column;

  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 2rem;
    background-color: #fff;
    border-bottom: 1px solid #e4e7ed;

    .header-left {
      .logo {
        font-size: 1.5rem;
        font-weight: bold;
        color: #303133;
        margin: 0;
      }
    }

    .header-right {
      .user-info {
        display: flex;
        align-items: center;
        gap: 0.5rem;

        .user-avatar {
          width: 24px;
          height: 24px;
          border-radius: 50%;
          object-fit: cover;
        }

        .user-name {
          font-weight: 500;
          color: #303133;
        }

        .user-id {
          font-size: 0.8rem;
          color: #909399;
        }
      }
    }
  }

  .main-content {
    display: flex;
    flex: 1;

    .sidebar {
      width: 200px;
      background-color: #fff;
      border-right: 1px solid #e4e7ed;
      padding: 1rem 0;
      flex-shrink: 0;

      .nav-menu {
        ul {
          list-style: none;
          padding: 0;
          margin: 0;

          .nav-item {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            padding: 0.75rem 1.5rem;
            cursor: pointer;
            transition: all 0.3s;
            color: #606266;

            &:hover {
              background-color: #f5f7fa;
              color: #409eff;
            }

            &.active {
              background-color: #ecf5ff;
              color: #409eff;
              font-weight: 500;
            }

            .nav-icon {
              font-size: 1rem;
            }

            .nav-text {
              font-size: 0.9rem;
            }
          }
        }
      }
    }

    .main-content-area {
      flex: 1;
      display: flex;
      flex-direction: column;

      .breadcrumb {
        padding: 1rem 2rem;
        background-color: #f5f7fa;
        border-bottom: 1px solid #e4e7ed;
        font-size: 0.9rem;
        color: #606266;
      }

      .content-area {
        display: flex;
        gap: 2rem;
        padding: 2rem;
        background-color: #f5f7fa;
        flex: 1;

        .form-section {
          flex: 1;
          background-color: #fff;
          padding: 2rem;
          border-radius: 8px;
          box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);

          .section-title {
            font-size: 1.5rem;
            font-weight: bold;
            margin-bottom: 2rem;
            color: #303133;
          }

          .form-step {
            display: flex;
            gap: 1rem;
            margin-bottom: 2rem;

            .step-number {
              width: 24px;
              height: 24px;
              border-radius: 50%;
              background-color: #409eff;
              color: #fff;
              display: flex;
              justify-content: center;
              align-items: center;
              font-size: 0.8rem;
              font-weight: bold;
              flex-shrink: 0;
              margin-top: 0.25rem;
            }

            .step-content {
              flex: 1;

              .step-title {
                font-size: 1rem;
                font-weight: 500;
                margin-bottom: 0.75rem;
                color: #303133;
              }

              .url-input {
                display: flex;
                align-items: center;
                gap: 0.5rem;

                .url-prefix {
                  font-weight: 500;
                  color: #303133;
                }

                .disabled-input {
                  opacity: 0.6;
                }
              }

              .upload-container {
                display: flex;
                gap: 1rem;
                margin-bottom: 0.5rem;

                .upload-item {
                  display: flex;
                  flex-direction: column;
                  align-items: center;
                  gap: 0.5rem;

                  .upload-box {
                    width: 100px;
                    height: 100px;
                    border: 2px dashed #dcdfe6;
                    border-radius: 8px;
                    display: flex;
                    justify-content: center;
                    align-items: center;
                    cursor: pointer;
                    transition: all 0.3s;
                    overflow: hidden;

                    &:hover {
                      border-color: #409eff;
                      background-color: #ecf5ff;
                    }

                    &.background-upload {
                      width: 200px;
                      height: 100px;
                    }

                    .upload-placeholder {
                      font-size: 2rem;
                      color: #c0c4cc;
                    }

                    .upload-image {
                      width: 100%;
                      height: 100%;
                      object-fit: cover;
                    }
                  }

                  .upload-label {
                    font-size: 0.8rem;
                    color: #606266;
                  }
                }
              }

              .social-media-icons {
                display: flex;
                gap: 1rem;
                margin-bottom: 1rem;

                .social-icon-wrapper {
                  cursor: pointer;
                  padding: 0.5rem;
                  border-radius: 4px;
                  transition: all 0.3s;

                  &:hover {
                    background-color: #f5f7fa;
                  }

                  .social-icon {
                    font-size: 1.5rem;
                    color: #c0c4cc;
                    transition: all 0.3s;

                    &.active {
                      color: #409eff;
                    }
                  }
                }
              }

              .social-media-inputs {
                margin-bottom: 1rem;

                .social-input-item {
                  display: flex;
                  align-items: center;
                  gap: 0.5rem;
                  margin-bottom: 0.75rem;

                  .social-input-icon {
                    font-size: 1rem;
                    color: #606266;
                  }

                  .social-input {
                    flex: 1;
                  }
                }
              }

              .tags-container {
                display: flex;
                flex-wrap: wrap;
                gap: 0.5rem;
                margin-bottom: 0.5rem;
              }
            }
          }

          .continue-button-container {
            display: flex;
            justify-content: flex-start;
            margin-top: 2rem;

            .continue-button {
              padding: 0.75rem 2rem;
              font-size: 1rem;
            }
          }
        }

        .preview-section {
          width: 400px;
          background-color: #fff;
          padding: 2rem;
          border-radius: 8px;
          box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);

          .preview-card {
            position: relative;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);

            .preview-background {
              width: 100%;
              height: 150px;
              background-color: #409eff;
              background-size: cover;
              background-position: center;
            }

            .preview-profile {
              padding: 1rem;
              text-align: center;
              position: relative;
              top: -50px;

              .avatar-container {
                position: relative;
                display: inline-block;
                margin-bottom: 1rem;

                .preview-avatar {
                  width: 100px;
                  height: 100px;
                  border-radius: 10%;
                  border: 4px solid #fff;
                  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
                  cursor: pointer;
                  object-fit: cover;
                }

                .avatar-edit-icon {
                  position: absolute;
                  bottom: 5px;
                  right: 5px;
                  background-color: #409eff;
                  color: #fff;
                  width: 24px;
                  height: 24px;
                  border-radius: 50%;
                  display: flex;
                  justify-content: center;
                  align-items: center;
                  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.15);
                  cursor: pointer;
                  transition: all 0.3s;

                  &:hover {
                    background-color: #66b1ff;
                  }
                }
              }

              .preview-username {
                font-size: 1.25rem;
                font-weight: bold;
                margin-bottom: 0.5rem;
                color: #303133;
              }

              .preview-about {
                font-size: 0.9rem;
                color: #606266;
                margin-bottom: 1rem;
                line-height: 1.5;
              }

              .preview-social-links {
                display: flex;
                justify-content: center;
                gap: 1rem;

                .preview-social-link {
                  color: #606266;
                  font-size: 1.25rem;
                  transition: all 0.3s;

                  &:hover {
                    color: #409eff;
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
</style>