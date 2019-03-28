Activity.dispatchTouchEvent(MotionEvent ev) -> 
PhoneWindow.superDispatchTouchEvent(MotionEvent event) ->
DecorView.superDispatchTouchEvent(MotionEvent event) ->

ViewGroup.dispatchTouchEvent(MotionEvent ev) ->
ViewGroup.onInterceptTouchEvent(MotionEvent ev) ->
ViewGroup.dispatchTransformedTouchEvent ->
child.dispatchTouchEvent(MotionEvent ev) ->

View.dispatchTouchEvent(MotionEvent ev) ->
OnTouchListener.onTouch(View v, MotionEvent event) -> 
View.onTouchEvent(MotionEvent event) ->
ListenerInfo.mOnClickListener.onClick(View)

----
总结：
- 如果父控件对触摸事件过程中的ACTION_DOWN动作进行了拦截，那么ACTION_DOWN动作不会再传递到子控件，并且
此事件过程的后续一系列动作（如move，up动作）都不会再传递给子控件。

- 如果子控件没有消费掉ACTION_DOWN动作，那么此事件过程的后续一系列动作也都不会再传递给该子控件。

- 如果子控件消费掉了ACTION_DOWN动作，但没有消费掉其他事件动作，那么其他事件动作不会再由上级父控件处理，
而是直接交给Activity来处理（调用Activity的onTouchEvent方法）

- 子控件要想通过调用mParent.requestDisallowInterceptToucheEvent(boolean)方法，向父控件申请取消对
事件的拦截，则必须要满足一个前提条件：那就是本次触摸事件过程中的ACTION_DOWN动作要传递给子控件，并由子
控件消费掉。

----
Activity.java

	public boolean dispatchTouchEvent(MotionEvent ev) {
        if (ev.getAction() == MotionEvent.ACTION_DOWN) {
            onUserInteraction();
        }
        if (getWindow().superDispatchTouchEvent(ev)) {
            return true;
        }
        return onTouchEvent(ev);
    }

PhoneWindow.java

	@Override
    public boolean superDispatchTouchEvent(MotionEvent event) {
        return mDecor.superDispatchTouchEvent(event);
    }

DecorView.java

	public boolean superDispatchTouchEvent(MotionEvent event) {
        return super.dispatchTouchEvent(event);
    }

ViewGroup.java

	public boolean dispatchTouchEvent(MotionEvent ev) {
		...
		boolean handled = false;

        if (onFilterTouchEventForSecurity(ev)) {

			final int action = ev.getAction();
            final int actionMasked = action & MotionEvent.ACTION_MASK;

            if (actionMasked == MotionEvent.ACTION_DOWN) {
                cancelAndClearTouchTargets(ev);
                resetTouchState();
            }
            ...
            // Check for interception.
            final boolean intercepted;

            if (actionMasked == MotionEvent.ACTION_DOWN
                    || mFirstTouchTarget != null) {

                final boolean disallowIntercept = (mGroupFlags & 
						FLAG_DISALLOW_INTERCEPT) != 0;

                if (!disallowIntercept) {
                    intercepted = onInterceptTouchEvent(ev);
                    ev.setAction(action); // restore action in case it was changed
                } else {
                    intercepted = false;
                }
            } else {
                // There are no touch targets and this action is not an initial down
                // so this view group continues to intercept touches.
                intercepted = true;
            }
			
			if (!canceled && !intercepted) {
                ...
                if (actionMasked == MotionEvent.ACTION_DOWN
                        || (split && actionMasked == MotionEvent.ACTION_POINTER_DOWN)
                        || actionMasked == MotionEvent.ACTION_HOVER_MOVE) {
                    ...
                    final int childrenCount = mChildrenCount;
                    if (newTouchTarget == null && childrenCount != 0) {
                        ...
                        final View[] children = mChildren;
                        for (int i = childrenCount - 1; i >= 0; i--) {
                            final int childIndex = getAndVerifyPreorderedIndex(
                                    childrenCount, i, customOrder);
                            final View child = getAndVerifyPreorderedView(
                                    preorderedList, children, childIndex);
								
							...
							if (!canViewReceivePointerEvents(child)
                                    || !isTransformedTouchPointInView(x, y, child, null)){
                                ev.setTargetAccessibilityFocus(false);
                                continue;
                            }
                            ...
                            if (dispatchTransformedTouchEvent(ev, false, child,
								 idBitsToAssign)) {
                                ...
                            }
                            ...
                        }
                        ...
                    }
                    ...
                }
            }

			if (mFirstTouchTarget == null) {
                // No touch targets so treat this as an ordinary view.
                handled = dispatchTransformedTouchEvent(ev, canceled, null,
                        TouchTarget.ALL_POINTER_IDS);
            }
			...
		}
		...
		return handled
	}

	private boolean dispatchTransformedTouchEvent(MotionEvent event, boolean cancel,
            View child, int desiredPointerIdBits) {
		final boolean handled;
		...
		// Calculate the number of pointers to deliver.
        final int oldPointerIdBits = event.getPointerIdBits();
        final int newPointerIdBits = oldPointerIdBits & desiredPointerIdBits;
		...
		final MotionEvent transformedEvent;
        if (newPointerIdBits == oldPointerIdBits) {
            ...
            transformedEvent = MotionEvent.obtain(event);
        } else {
            transformedEvent = event.split(newPointerIdBits);
        }
        ...
        // Perform any necessary transformations and dispatch.

        if (child == null) {

            handled = super.dispatchTouchEvent(transformedEvent);

        } else {
            ...
            handled = child.dispatchTouchEvent(transformedEvent);
        }

        // Done.
        transformedEvent.recycle();
        return handled;
	}

	private void resetTouchState() {
        clearTouchTargets();
        resetCancelNextUpFlag(this);
        mGroupFlags &= ~FLAG_DISALLOW_INTERCEPT;
        mNestedScrollAxes = SCROLL_AXIS_NONE;
    }

	@Override
    public void requestDisallowInterceptTouchEvent(boolean disallowIntercept) {
        ...
        if (disallowIntercept) {
            mGroupFlags |= FLAG_DISALLOW_INTERCEPT;
        } else {
            mGroupFlags &= ~FLAG_DISALLOW_INTERCEPT;
        }

        // Pass it up to our parent
        if (mParent != null) {
            mParent.requestDisallowInterceptTouchEvent(disallowIntercept);
        }
    }

View.java

	public boolean dispatchTouchEvent(MotionEvent event) {
        ...
		boolean result = false;
		...
        if (onFilterTouchEventForSecurity(event)) {
            ...
            ListenerInfo li = mListenerInfo;
            if (li != null && li.mOnTouchListener != null
                    && (mViewFlags & ENABLED_MASK) == ENABLED
                    && li.mOnTouchListener.onTouch(this, event)) {

                result = true;
            }

            if (!result && onTouchEvent(event)) {
                result = true;
            }
        }
        ...
        return result;
    }

	public boolean onTouchEvent(MotionEvent event) {
		...
		if (mTouchDelegate != null) {
            if (mTouchDelegate.onTouchEvent(event)) {
                return true;
            }
        }

        if (clickable || (viewFlags & TOOLTIP) == TOOLTIP) {

			switch (action) {
				case MotionEvent.ACTION_UP:
				...
				performClick();
				...
				break;
				...
			}
		
			return true;
		}
		return false;
	}

	public boolean performClick() {
        final boolean result;
        final ListenerInfo li = mListenerInfo;
        if (li != null && li.mOnClickListener != null) {

            playSoundEffect(SoundEffectConstants.CLICK);
            li.mOnClickListener.onClick(this);

            result = true;
        } else {
            result = false;
        }
		...
        return result;
    }