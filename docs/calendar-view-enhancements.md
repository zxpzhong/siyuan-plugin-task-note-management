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

## 进度
- [x] 设计确认与代码框架扫描完成。
- [x] 双击空白日期直接创建提醒（起止日期一致）。
- [x] 双击未绑定日程自动创建文档并绑定。
- [x] 明确开启拖拽移动与边界调整配置。
- [x] 编译验证（多次，分阶段）。
