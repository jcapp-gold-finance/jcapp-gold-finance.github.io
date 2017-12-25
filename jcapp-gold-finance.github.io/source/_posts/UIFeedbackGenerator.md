title: UIFeedbackGenerator 翻译一篇
date: 2017/12/25 10:00:00

type: iOS
---

> Interpreter：戴奕





iPhone 7 之后的机型中，苹果使用了最新的 Taptic Engine 震动引擎，该引擎能够模拟不同的振感，因此系统在人机交互上达到了一个新的顶端。当然该功能同样以 API 形式开放给了 iOS 开发人员，本篇文章是对 UIFeedbackGenerator API 以及相关 人机交互指南的翻译。



<!-- more -->



Class

## UIFeedbackGenerator

The abstract superclass for all feedback generators.

该类是所有反馈产生器的抽象类.



## Overview（概览）

Do not subclass or create instances of this class yourself. Instead, instantiate one of the provided concrete subclasses:

[`UIImpactFeedbackGenerator`](https://developer.apple.com/reference/uikit/uiimpactfeedbackgenerator?language=objc). Use impact feedback generators to indicate that an impact has occurred. For example, you might trigger impact feedback when a user interface object collides with something or snaps into place.

[`UISelectionFeedbackGenerator`](https://developer.apple.com/reference/uikit/uiselectionfeedbackgenerator?language=objc). Use selection feedback generators to indicate a change in selection.

[`UINotificationFeedbackGenerator`](https://developer.apple.com/reference/uikit/uinotificationfeedbackgenerator?language=objc). Use notification feedback generators to indicate successes, failures, and warnings.



不要自己创建该类的实例或者子类。你应该使用已经提供的子类：

[`UIImpactFeedbackGenerator`](https://developer.apple.com/reference/uikit/uiimpactfeedbackgenerator?language=objc). 使用碰撞反馈发生器来表明发生了碰撞。举个例子，当用户界面上的一个对象碰到什么东西、或者卡到某个地方的时候，触发碰撞反馈发生器。

[`UISelectionFeedbackGenerator`](https://developer.apple.com/reference/uikit/uiselectionfeedbackgenerator?language=objc). 使用选择反馈发生器来表明选择状态发生了改变。

[`UINotificationFeedbackGenerator`](https://developer.apple.com/reference/uikit/uinotificationfeedbackgenerator?language=objc). 使用通知反馈发生器来表明成功，失败，警告。



### Using Feedback Generators

Haptic feedback provides a tactile response, such as a tap, that draws attention and reinforces both actions and events. While many system-provided interface elements (for example, pickers, switches, and sliders) automatically provide haptic feedback, you can use feedback generators to add your own feedback to custom views and controls.



触觉反馈提供触觉响应，比如敲击，这会吸引用户的注意力并增强了行动和事件。许多系统提供的界面元素（例如选择器、开关、滑块）都提供了触觉反馈，你可以把反馈生成器添加到你自定义的视图和控件中。



>  Tip
>
>  If you want to include sound along with the haptic feedback, you need to manually play the sound and sync it with the haptics.



> 提示
>
> 如果你想在触觉反馈的同时播放声音，你需要手动播放声音并与触觉同步。



When providing feedback:

- Always use feedback for its intended purpose. Don’t select a haptic because of the way it feels.
- The source of the feedback must be clear to the user. For example, the feedback must match a visual change in the user interface, or must be in response to a user action. Feedback should never come as a surprise.
- Don’t overuse feedback. Overuse can cause confusion and diminish the feedback’s significance.





当你提供反馈时：

* 把反馈用在该用的地方上。不要因为触觉反馈而使用触觉反馈。
* 必须让用户清除反馈的来源。举个例子，触觉反馈必须和视觉的改变匹配，或者必须是响应用户的操作。不要把反馈当做“惊喜”给用户。
* 不要过度使用反馈。过度使用会变得混乱并会减少反馈的意义。





For additional guidance on when and how to use feedback generators, see [Haptic Feedback](https://developer.apple.com/ios/human-interface-guidelines/interaction/feedback/#haptics) in [iOS Human Interface Guidelines](https://developer.apple.com/ios/human-interface-guidelines/).

有关如何使用反馈发生器的更多指导，请查看 [Haptic Feedback](https://developer.apple.com/ios/human-interface-guidelines/interaction/feedback/#haptics) in [iOS Human Interface Guidelines](https://developer.apple.com/ios/human-interface-guidelines/)。



To use a feedback generator, the following are required:

1. [Instantiating the Generator](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2555404)
2. [Preparing the Generator](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2555405) (optional)
3. [Triggering Feedback](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2555415)
4. [Releasing the Generator](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2555460) (optional).



要使用反馈发生器，下列几点是必须的：

1. 初始化发生器
2. 准备好发生器（可选）
3. 触发发生器
4. 释放发生器（可选）





Listing 1 demonstrates triggering selection feedback from a pan gesture recognizer.

**Listing 1** Triggering selection feedback

```objective-c
- (IBAction)gestureHandler:(UIPanGestureRecognizer *)sender {
    
    switch (sender.state) {
        case UIGestureRecognizerStateBegan:
            
            // Instantiate a new generator.
            self.feedbackGenerator = [[UISelectionFeedbackGenerator alloc] init];
            
            // Prepare the generator when the gesture begins.
            [self.feedbackGenerator prepare];
            
            break;
            
        case UIGestureRecognizerStateChanged:
            
            // Check to see if the selection has changed...
            if ([self myCustomHasSelectionChangedMethodWithTranslation:[sender translationInView: self.view]]) {
                
                // Trigger selection feedback.
                [self.feedbackGenerator selectionChanged];
                
                // Keep the generator in a prepared state.
                [self.feedbackGenerator prepare];
    
            }
            
            break;
            
        case UIGestureRecognizerStateCancelled:
        case UIGestureRecognizerStateEnded:
        case UIGestureRecognizerStateFailed:
            
            // Release the current generator.
            self.feedbackGenerator = nil;
            
            break;
            
        default:
            
            // Do nothing.
            break;
    }
}
```



#### Instantiating the Generator

To create feedback, you must first instantiate one of the [`UIFeedbackGenerator`](https://developer.apple.com/reference/uikit/uifeedbackgenerator?language=objc) class’s concrete subclasses ([`UIImpactFeedbackGenerator`](https://developer.apple.com/reference/uikit/uiimpactfeedbackgenerator?language=objc), [`UISelectionFeedbackGenerator`](https://developer.apple.com/reference/uikit/uiselectionfeedbackgenerator?language=objc), or [`UINotificationFeedbackGenerator`](https://developer.apple.com/reference/uikit/uinotificationfeedbackgenerator?language=objc)).

[Listing 1](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2576673) instantiates a new [`UISelectionFeedbackGenerator`](https://developer.apple.com/reference/uikit/uiselectionfeedbackgenerator?language=objc) object whenever a pan gesture begins.



如果要创建一个反馈，首先你必须从指定的 [`UIFeedbackGenerator`](https://developer.apple.com/reference/uikit/uifeedbackgenerator?language=objc)子类中创建。

清单1是在移动手势开始时创建了一个[`UISelectionFeedbackGenerator`](https://developer.apple.com/reference/uikit/uiselectionfeedbackgenerator?language=objc) 实例。



#### Preparing the Generator

Preparing the generator can reduce latency when triggering feedback. This is particularly important when trying to match feedback to sound or visual cues.

Calling the generator’s [`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc) method puts the Taptic Engine in a prepared state. To preserve power, the Taptic Engine stays in this state for only a short period of time (on the order of seconds), or until you next trigger feedback.

Think about when and where you can best prepare your generators. If you call [`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc) and then immediately trigger feedback, the system won’t have enough time to get the Taptic Engine into the prepared state, and you may not see a reduction in latency. On the other hand, if you call [`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc) too early, the Taptic Engine may become idle again before you trigger feedback.

[Listing 1](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2576673) prepares the generator when the pan gesture begins, and then triggers feedback when the gesture changes.

For more information, see the [`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc) method.



让发生器做好准备可以减少触发时的延迟。这点在与声音、视觉匹配时尤其重要。

调用[`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc)让Taptic引擎处于准备状态。为了节约电量，Taptic引擎只会在短时间内保持此状态（数秒），或者直到下一次触发反馈。

想清楚什么时候，在哪里准备发生器是最好的。如果你调用了[`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc)然后马上触发反馈，系统可能来不及让Taptic引擎进入准备状态，所以你可能看不到延迟的减少。另一方面，如果你调用[`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc)的实际太早，那么Taptic引擎可能在你触发反馈之前又恢复了闲置状态。

清单1是在手势开始时准备好发生器，然后在手势位置改变时触发反馈。

要获取更多的信息，查阅[`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc)方法。



#### Triggering Feedback

Each feedback generator subclass has a unique triggering method. To trigger feedback, call the appropriate method: [`impactOccurred`](https://developer.apple.com/reference/uikit/uiimpactfeedbackgenerator/2374287-impactoccurred?language=objc), [`selectionChanged`](https://developer.apple.com/reference/uikit/uiselectionfeedbackgenerator/2374284-selectionchanged?language=objc), or [`notificationOccurred:`](https://developer.apple.com/reference/uikit/uinotificationfeedbackgenerator/2369826-notificationoccurred?language=objc).

Note that calling these methods does not play haptics directly. Instead, it informs the system of the event. The system then determines whether to play the haptics based on the device, the application’s state, the amount of battery power remaining, and other factors.

For example, haptic feedback is currently played only:

- On a device with a supported Taptic Engine (iPhone 7 and iPhone 7 Plus).
- When the app is running in the foreground.
- When the System Haptics setting is enabled.

As a general rule, trust the system to determine whether it should play feedback. Don't check the device type or app state to conditionally trigger feedback. After you’ve decided how you want to use feedback, always trigger it when the appropriate events occur. The system ignores any requests that it cannot fulfill.

In [Listing 1](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2576673), each time the gesture changes the gesture handler checks to see if the selection has changed. If so, the handler triggers feedback and prepares for the next change.



每一个发生器的子类都有自己独特的触发方法。去触发反馈，去调用适当的方法：[`impactOccurred`](https://developer.apple.com/reference/uikit/uiimpactfeedbackgenerator/2374287-impactoccurred?language=objc), [`selectionChanged`](https://developer.apple.com/reference/uikit/uiselectionfeedbackgenerator/2374284-selectionchanged?language=objc), or [`notificationOccurred:`](https://developer.apple.com/reference/uikit/uinotificationfeedbackgenerator/2369826-notificationoccurred?language=objc)。

请注意，调用这些方法不会立即触发触觉反馈。而是把该事件通知系统。系统会根据设备、应用程序状态、剩余电量以及其他因素类决定是否播放触觉。

举个例子，只有在以下几个情况触觉反馈会立即触发：

* 在一个支持Taptic Engine的设备上（iPhone 7 and iPhone 7 Plus）。
* 应用程序在前台运行。
* 设置->声音与触觉->系统触觉反馈 选项打开。

作为一般规则，应该信任系统来决定是否播放反馈。不要在触发反馈之前检查设备类型与应用的状态。当你决定好想如何使用反馈，那么在合适的事件发生时都要去触发反馈（而不用去判断）。系统会忽略任何不能满足的请求。

在清单1中，任何时候当手势位置改变时都会去检查选中状态。如果是选中，那么就会触发反馈并且会为下一次改变将发生器置为准备状态。



#### Releasing the Generator

If you no longer need a prepared generator, remove all references to the generator object and let the system deallocate it. This lets the Taptic Engine return to its idle state.

In [Listing 1](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2576673), the pan gesture recognizer releases its generator when the gesture ends, fails, or is canceled. Assigning `nil` to the instance variable removes the reference to the old generator. If there are no other references to the generator, the system deallocates it.



如果你不再需要准备发生器，那么把该对象所有的引用移除掉，让系统释放该对象。这会让Taptic引擎回到闲置状态。

在清单1中，在手势对象结束/失败/取消时都会释放发生器。设置会nil会移除对旧发生器的引用。如果没有别的引用了，系统就会销毁这个对象。





Instance Method

## prepare

Prepares the generator to trigger feedback.

发生器置为触发做准备 



## Discussion

When you call this method, the generator is placed into a prepared state for a short period of time. While the generator is prepared, you can trigger feedback with lower latency.

Think about when you can best prepare your generators. Call `prepare` before the event that triggers feedback. The system needs time to prepare the Taptic Engine for minimal latency. Calling [`prepare`](https://developer.apple.com/reference/uikit/uifeedbackgenerator/2369818-prepare?language=objc) and then immediately triggering feedback (without any time in between) does not improve latency.

To conserve power, the Taptic Engine returns to an idle state after any of the following events:

- You trigger feedback on the generator.
- A short period of time passes (typically seconds).
- The generator is deallocated.

After feedback is triggered, the Taptic Engine returns to its idle state. If you might trigger additional feedback within the next few seconds, immediately call `prepare` to keep the Taptic Engine in the prepared state.

You can also extend the prepared state by repeatedly calling the `prepare` method. However, if you continue calling `prepare` without ever triggering feedback, the system may eventually place the Taptic Engine back in an idle state and ignore any further `prepare` calls until after you trigger feedback at least once.

If you no longer need a prepared generator, remove all references to the generator object and let the system deallocate it. This lets the Taptic Engine return to its idle state.



当你调用这个方法，发生器会在短时间内置为准备状态。在准备状态期间触发的话会降低延迟。

想清楚在最合适的实际去准备发生器。在触发反馈之前调用`prepare` 方法。系统为了降低延迟需要一些时间去准备Taptic引擎。调用`prepare` 方法然后立即触发并不会立即降低延迟（没有任何时间间隔）。

为了节约电量，Taptic引擎会在下列几种情况下回到闲置状态：

* 触发了反馈。
* 过去了一小段时间（一般为几秒）。
* 发生器销毁了。

在触发过后，Taptic引擎会回到闲置状态。如果你在接下来的几秒钟可能再次触发反馈，那么久立即调用`prepare`方法来让Taptic引擎进入准备状态。

你还可以通过反复调用`prepare`方法来延长准备状态。然而，如果你一直没有触发反馈，但还是继续调用`prepare`方法，那么系统会将Taptic引擎置为闲置状态，并且忽视你的`prepare`方法，除非你触发一次反馈。

如果你再也不需要准备一个发生器，移除所有的引用让系统销毁它。这会让Taptic引擎置为闲置状态。



> Note
>
> The `prepare` method is optional; however, it is highly recommended. Calling this method helps ensure that your feedback has the lowest possible latency.



> 注意点
>
> `prepare`是可选的，但是是极力推荐的。调用这个方法有助于确保反馈的及时性。







Class

## UIImpactFeedbackGenerator

A concrete [`UIFeedbackGenerator`](https://developer.apple.com/reference/uikit/uifeedbackgenerator?language=objc) subclass that creates haptics to simulate physical impacts.

一个用来创建模拟物理碰撞触觉的 [`UIFeedbackGenerator`](https://developer.apple.com/reference/uikit/uifeedbackgenerator?language=objc)具体子类。



## Overview

Use impact feedback to indicate that an impact has occurred. For example, you might trigger impact feedback when a user interface object collides with another object or snaps into place.

For more information, see [Using Feedback Generators](https://developer.apple.com/reference/uikit/uifeedbackgenerator#2555399).

使用碰撞反馈来表明发生了一个碰撞。举个例子，当用户界面上的一个对象碰到什么东西、或者卡到某个地方的时候，触发碰撞反馈发生器。



## Symbols

### Initializers

[`- initWithStyle:`](https://developer.apple.com/reference/uikit/uiimpactfeedbackgenerator/2374286-initwithstyle?language=objc)Returns a newly initialized feedback generator.





Instance Method

## Parameters

- `style`

  A value representing the mass of the colliding objects. For a list of valid feedback styles, see the [`UIImpactFeedbackStyle`](https://developer.apple.com/reference/uikit/uiimpactfeedbackstyle?language=objc) enumeration.

  一个代表碰撞对象的质量的值。







## iOS Human Interface Guidelines



## Feedback

Feedback helps people know what an app is doing, discover what they can do next, and understand the results of actions.

反馈帮助人们知道应用现在在做什么，知道接下来该做什么，理解事件的结果。



**Unobtrusively integrate status and other types of feedback into your interface.** Ideally, users can get important information without taking action or being interrupted. Mail, for example, subtly displays status information in the toolbar while navigating through mailboxes of messages. This information doesn’t compete with the primary content onscreen, but can be checked at any time with a quick glance.

**将状态与其他类型的反馈不间断地集成到你的界面中。**理想情况下，用户可以再不中断动作的情况下获取重要信息。例如，“邮箱”在导航邮件时，巧妙地将当前的状态信息显示在（下方）工具栏中。这个内容不会喧宾夺主，但是可以快速地获取相关信息。



**Avoid unnecessary alerts.** An alert is a powerful feedback mechanism, but should be used only to deliver important—and ideally actionable—information. If people see too many alerts that don’t contain essential information, they quickly learn to ignore future alerts. For additional guidance, see [Alerts](https://developer.apple.com/ios/human-interface-guidelines/ui-views/alerts/).

**避免不必要的弹框。**弹框是一种强有力的反馈手段，但是只能够在传递重要信息以及理想的信息时进行使用。如果人们经常看到没有必要信息的弹框，他们很快就会学会忽略弹框。要获取更多信息，请查阅 [Alerts](https://developer.apple.com/ios/human-interface-guidelines/ui-views/alerts/).



### Haptic Feedback

On supported devices, haptics provide a way to physically engage users with tactile feedback that gets attention and reinforces actions. Some system-provided interface elements, such as pickers, switches, and sliders, automatically provide haptic feedback as users interact with them. Your app can also ask the system to generate different types of haptic feedback. iOS manages the strength and behavior of this feedback.

在支持的设备上，触觉提供了一种可以吸引用户注意力并加强重视的一种反馈方式。一些系统控件已经提供了这种功能，例如选择器、开关、进度滑竿等，在用户使用这些控件交互时会自动提供触觉反馈。你的应用程序也可以要求系统产生不同类型的触觉反馈。iOS系统会管理这种反馈的强度和行为。



##### Notification（通知）

* Success：指示任务或动作，例如存入支票、解锁车辆等已经完成
* Warning：表示任务或动作，例如存入支票、解锁车辆等，发出某种警告
* Failure：表示任务或动作，例如存入支票、解锁车辆等，已经失败

##### Impact（影响）

* Light：提供一个给视觉补充的触觉反应。例如，当一个视图滑入或者两个物体碰撞时，用户会觉得砰的一声
* Medium：同上
* Heavy：同上

##### Selection（选择）

* Selection：当一个选择器正在改变时提供提示。例如，当滚动一个选择器时，用户会感觉到轻轻的敲击声。这种反馈旨在通过一系列离散值来传递运动，而不是制定或确认一个选择。





**Use haptics judiciously.** Overuse can cause confusion and diminish the significance of feedback.

明智地使用触觉反馈。过度使用会导致混乱，削弱反馈的重要性。



**In general, provide haptic feedback in response to user-initiated actions.** It’s easy for people to correlate haptics with actions they initiated. Arbitrary feedback can feel disconnected and be misinterpreted.

一般来说，在用户发起的动作时提供触觉反馈。人们很容易将他们的动作与触觉关联在一起。随意的触觉反馈会导致误解和没有联系。



**Don’t redefine feedback types.** To ensure a consistent experience, use feedback types as intended. Don’t, for example, use "impact" feedback to notify the user that a task has succeeded. Instead, use the "success" variation of "notification" feedback.

不要重新第一反馈类型。为了保持体验的一致性，请使用预期的反馈类型。例如不要使用 "impact" 充当一个任务完成的反馈，应该使用 "notification" 。



**Fine tune your visual experience for haptics.** Provide visual and haptic feedback together to create a deeper connection between actions and results. Make sure animations are sharp and precise, to visually match what the user feels.

协调你的触觉、视觉体验。一起使用视觉、触觉反馈来加强动作和结果之间的联系。视觉上确保动画清晰和精确以匹配用户的感受。



**Don’t rely on a single mode of communication.** Not all devices support the full range of haptic feedback, and people can disable the feature entirely in Settings if they choose. In addition, haptic feedback occurs only when the device is active and your app is frontmost. Supplement haptics with visual and audible cues to ensure that important information isn’t missed.

不要依赖于一种单一的沟通方式。不是所有的设备都支持全方位的触觉反馈，同时用户可以再设置中选择禁用这项特性。另外，触觉反馈只有在你的设备是开启状态并且你的应用是处于前台。视觉中补充触觉反馈以确保重要的信息不被错过。



**Use haptics when visual feedback may be occluded.** Some interactions, such as dragging an object to a location onscreen, are hidden by the user’s finger. Consider generating feedback that lets the user know when they’ve reached a particular location or value.

当视觉反馈可能被遮挡时，使用触觉反馈。一些交互，如将对象拖动到屏幕上的位置，会被用户的手指所遮挡。考虑使用触觉反馈来告诉用户到达了指定位置或者值。



**Prepare the system before initiating feedback.** Because there may be some latency involved when providing haptic feedback, it’s best to get the system ready shortly before requesting the feedback. Otherwise, the haptics might come too late and feel disconnected from the user's actions or what they’re seeing on the screen.

在启动反馈之前让系统做准备。因为在提供触觉反馈时可能会有一些延迟，所以最好在请求反馈之前尽快准备好系统。否则发生触觉时可能太迟了，会导致用户无法将当前屏幕上的动作和反馈联系起来。



**Synchronize haptics with accompanying sound.** Haptics don’t automatically synchronize with sounds. If you want an accompanying sound, you’re responsible for synchronizing it.

将触觉与声音同步。触觉不会自动同步声音。如果你想要同步发出声音，你需要负责同步这个事情。
