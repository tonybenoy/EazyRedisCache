from datetime import datetime, timedelta
from django.core.cache import cache
from functools import _make_key, wraps
import functools
from frozendict import frozendict


def freezeargs(func):
    """Transform mutable dictionnary
    Into immutable
    Useful to be compatible with cache
    """

    @functools.wraps(func)
    def wrapped(*args, **kwargs):
        args = tuple(
            [frozendict(arg) if isinstance(arg, dict) else arg for arg in args]
        )
        kwargs = {
            k: frozendict(v) if isinstance(v, dict) else v
            for k, v in kwargs.items()
        }
        return func(*args, **kwargs)

    return wrapped


def get_or_set_cache(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        key_suffix = _make_key(args, kwargs, typed=False)
        key = f"{func.__name__}{key_suffix}"
        response = cache.get(key)
        if response:
            return response
        value = func(*args, **kwargs)
        cache.set(key, value)
        cache.expire_at(key, datetime.now() + timedelta(hours=1))
        return value

    return wrapper

