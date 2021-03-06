# Py.test Fixture Configuration

Simple configuration objects for Py.test fixtures. 
Allows you to skip tests when their required config variables aren't set.
                      
## Installation

Install using your favourite package manager:

```bash
    pip install pytest-fixture-config
    #  or..
    easy_install pytest-fixture-config
```

Enable the fixture explicitly in your tests or conftest.py (not required when using setuptools entry points):

```python
    pytest_plugins = ['pytest_fixture_config']
```


## Specifying Configuration

To specify your variables you create a class somewhere in your plugin module,
and a singleton instance of the class which reads the variables from wherever
you want. In this example we read them from the shell environment:

```python
    import os
    from pytest_fixture_config import Config

    class FixtureConfig(Config):
        __slots__ = ('log_dir', 'log_watcher')
        
    CONFIG=FixtureConfig(
        log_dir=os.getenv('LOG_DIR', '/var/log'),       # This has a default
        log_watcher=os.getenv('LOG_WATCHER'),           # This does not 
    )
```    

## Using Configuration

Simply reference the singleton at run-time in your fixtures:

```python
    import pytest
    
    @pytest.fixture
    def log_watcher():
        return subprocess.popen([CONFIG.log_watcher, '--log-dir', CONFIG.log_dir])
    
    def test_log_watcher(watcher):
        watcher.communicate()
```

