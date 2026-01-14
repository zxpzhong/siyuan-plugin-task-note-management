# 日历视图增强：设计与进度

## 代码框架扫描（摘要）
- 日历视图入口：`src/components/CalendarView.ts`，基于 FullCalendar 初始化视图、事件渲染与交互（点击、拖拽、缩放等）。
- 事件数据来源：`getAllReminders/saveReminders`（`src/utils/icsSubscription.ts`），负责合并本地提醒与订阅提醒。
- 事件呈现与交互：`eventClick/eventDrop/eventResize/dateClick/select` 等回调统一在 `CalendarView` 内处理。
- 绑定文档逻辑：`CalendarView.bindReminderToBlock` 负责将提醒绑定到块并更新书签状态；创建文档逻辑参考 `ReminderPanel.createDocumentAndBind` 与 `BlockBindingDialog` 的新建流程。

## 整体设计
1. **双击空白日期创建记录**
   - 在 `dateClick` 的双击检测中，直接创建一条提醒记录（起始/结束日期均为点击日期），不再弹出对话框。
   - 默认标题使用 `t("newTask")`，同时继承当前日历筛选（单选项目/分类）作为默认归属。

2. **双击未绑定日程自动创建文档并绑定**
   - 在 `eventClick` 内引入双击检测，双击时若日程无 `blockId` 且非订阅任务，则调用「新建文档并绑定」逻辑。
   - 新建文档使用插件设置的默认笔记本与路径模板（`newDocNotebook/newDocPath`），生成文档后调用 `bindReminderToBlock` 绑定。

3. **拖拽与拖拽边界编辑**
   - 保持 FullCalendar 的拖拽/调整大小能力，并显式开启 `eventStartEditable/eventDurationEditable` 以确保日程可拖拽移动与调整边界。

4. **新增修复与体验优化**
   - **月视图点击/拖拽区域修复**：限制 all-day 区域高度的样式仅作用于时间视图，避免影响月视图下半部分的点击/拖拽响应。
   - **拖拽后提示信息及时更新**：拖拽/调整后同步更新事件的扩展字段，避免悬停提示仍显示旧时间。
   - **单击打开方式优化**：日历内已绑定块任务单击时，直接在右侧分屏打开以保留日历视图。
   - **刷新性能优化**：使用 FullCalendar 的 batchRendering 合并渲染更新，减少不必要的全量 render。

## 进度
- [x] 设计确认与代码框架扫描完成。
- [x] 双击空白日期直接创建提醒（起止日期一致）。
- [x] 双击未绑定日程自动创建文档并绑定。
- [x] 明确开启拖拽移动与边界调整配置。
- [x] 编译验证（多次，分阶段）。
- [x] 月视图点击/拖拽区域修复完成。
- [x] 拖拽/调整后提示信息实时更新。
- [x] 日历单击任务使用右侧分屏打开。
- [x] 日历刷新性能优化处理。
