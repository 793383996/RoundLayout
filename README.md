同事写的圆角Layout处理，因为CardView处理有闪白屏和圆角毛边现象

            <declare-styleable name="RoundFrameLayout">
                <attr name="Round" format="dimension" />
            </declare-styleable>

        public class RoundFrameLayout extends FrameLayout {
        private final Path mPath;
        private final Paint mPaint;
        private final RectF mRectF;
        private final boolean isClipBackground;
        private float mRadius;

        @Keep
        public RoundFrameLayout(@NonNull Context context, @Nullable AttributeSet attrs) {
            super(context, attrs, 0);

            isClipBackground = true;

            mPath = new Path();
            mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
            mRectF = new RectF();

            mPaint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.DST_IN));

            TypedArray array = context.obtainStyledAttributes(attrs,
                    R.styleable.RoundFrameLayout, 0, 0);
            mRadius = array.getDimension(R.styleable.RoundFrameLayout_Round, 50f);
            array.recycle();
        }

        public void setRadius(float radius) {
            mRadius = radius;
            postInvalidate();
        }

        @Keep
        @Override
        protected void onSizeChanged(int w, int h, int oldw, int oldh) {
            super.onSizeChanged(w, h, oldw, oldh);
            mRectF.set(0, 0, w, h);
        }

        @Keep
        @Override
        public void draw(Canvas canvas) {
            super.draw(canvas);
            if (Build.VERSION.SDK_INT >= P) {
                draw28(canvas);
            } else {
                draw27(canvas);
            }
        }

        @Keep
        @Override
        protected void dispatchDraw(Canvas canvas) {
            if (Build.VERSION.SDK_INT >= P) {
                dispatchDraw28(canvas);
            } else {
                dispatchDraw27(canvas);
            }
        }

        private void draw27(Canvas canvas) {
            if (isClipBackground) {
                canvas.saveLayer(mRectF, null, Canvas.ALL_SAVE_FLAG);
                super.draw(canvas);
                canvas.drawPath(genPath(), mPaint);
                canvas.restore();
            } else {
                super.draw(canvas);
            }
        }

        private void draw28(Canvas canvas) {
            if (isClipBackground) {
                canvas.save();
                canvas.clipPath(genPath());
                super.draw(canvas);
                canvas.restore();
            } else {
                super.draw(canvas);
            }
        }

        private void dispatchDraw27(Canvas canvas) {
            canvas.saveLayer(mRectF, null, Canvas.ALL_SAVE_FLAG);
            super.dispatchDraw(canvas);
            canvas.drawPath(genPath(), mPaint);
            canvas.restore();
        }

        private void dispatchDraw28(Canvas canvas) {
            canvas.save();
            canvas.clipPath(genPath());
            super.dispatchDraw(canvas);
            canvas.restore();
        }

        private Path genPath() {
            mPath.reset();
            mPath.addRoundRect(mRectF, mRadius, mRadius, Path.Direction.CW);
            return mPath;
        }

    }



