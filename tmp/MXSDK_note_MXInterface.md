	package com.minxing.kit.internal.core;
	public enum MXInterface

	...,
	GET_PERSONAL_INFO("/api/v1/users/%s?include_user_info=true&is_followed_by=%s", HashMap.class),
	CONTACT_SEARCH("/api/v1/departments/search", Object.class),
	...;

	private String mInterface;
    private String formatFace;
    private Class<?> mClazz;

	private MXInterface(String var3, Class<?> var4) {
        this.mInterface = var3;
        this.formatFace = var3;
        this.mClazz = var4;
    }

	public String getFormatFace() { return this.formatFace; }

    public String getInsType() { return this.mInterface; }

    public Class<?> getClazz() { return this.mClazz; }

	public MXInterface insertParam(Object... var1) {
        if (var1 != null) {
            this.formatFace = String.format(this.mInterface, var1);
        } else {
            this.formatFace = this.mInterface;
        }

        return this;
    }