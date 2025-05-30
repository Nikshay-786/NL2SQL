# Professional NL2SQL Pipeline

Enterprise-grade Natural Language to SQL conversion system with statistical confidence calculation, professional logging, and robust configuration management.

## Overview

This production-ready NL2SQL pipeline converts natural language queries into SQL statements for Snowflake data warehouses. It features intelligent fallback strategies, statistical confidence calculation, and comprehensive logging for enterprise environments.

## Key Features

- **Statistical Confidence Calculation**: Data-driven confidence scores based on pattern usage, match quality, and execution success
- **Professional Logging**: Timestamped log files with centralized logging across all components
- **Configuration Management**: JSON-based configuration with environment variable overrides
- **Multi-Strategy Processing**: LLM generation with pattern matching fallback
- **Enterprise Security**: Built on existing BIU utilities framework
- **Robust Error Handling**: Graceful degradation with detailed error reporting

## Architecture

```
├── main.py                          # Main execution script
├── core/
│   ├── nl2sql_pipeline.py          # Core pipeline logic
│   └── pattern_matcher.py          # Advanced pattern matching
├── utils/
│   └── logger.py                   # Centralized logging
├── config/
│   ├── pipeline_config.py          # Configuration management
│   └── pipeline_config.json        # Default configuration
├── data/                           # Pattern statistics (auto-generated)
└── logs/                          # Log files (auto-generated)
```

## Installation

1. **Ensure dependencies are available**:
   ```cmd
   pip install -r requirements.txt
   ```

2. **Verify BIU utilities**:
   ```cmd
   # Ensure biuutilities.py is in the project directory
   dir biuutilities.py
   ```

3. **Optional: Configure LLM APIs**:
   ```cmd
   # Set environment variables for LLM access
   set OPENAI_API_KEY=your_key_here
   # OR
   set ANTHROPIC_API_KEY=your_key_here
   ```

## Usage

### 🚀 Quick Start Guide

#### 1. System Status Check (Recommended First Step)
```cmd
python main.py --status
```
This will show you:
- LLM availability
- Snowflake connectivity
- Pattern statistics
- Configuration settings
- Log file location

#### 2. Single Query Execution
```cmd
python main.py --query "How many active loans do we have?"
```

#### 3. Interactive Mode
```cmd
python main.py
```
Enter queries one by one. Type 'quit' to exit.

#### 4. Batch Processing
```cmd
python main.py --batch sample_queries.txt
```

#### 5. Offline Mode (Pattern Matching Only)
```cmd
python main.py --offline --query "Show me high risk loans"
```

#### 6. Help
```cmd
python main.py --help
```

## Configuration

### Environment Variables
- `DB_ENVIRONMENT`: Database environment (default: "prod")
- `DB_ROLE`: Database role (default: "PROD_BIU_READ_ONLY_NS")
- `DB_SCHEMA`: Database schema (default: "UNO_DS")
- `LLM_BASE_CONFIDENCE`: Base confidence for LLM results (default: 0.8)
- `LOG_LEVEL`: Logging level (default: "INFO")

### Configuration File
Edit `config/pipeline_config.json` to customize:
- Database connection parameters
- LLM confidence calculation weights
- Pattern matching thresholds
- Logging configuration

## Confidence Calculation

### Pattern Matching Confidence (Statistical)
```
confidence = (match_quality × 0.5) + (usage_frequency × 0.3) + ((1 - complexity) × 0.2)
```
Calculated using statistical analysis:
- **Match Quality** (50%): Regex strength + keyword overlap
- **Usage Frequency** (30%): Historical success rate + usage count
- **Pattern Complexity** (20%): Inverse of pattern complexity

### LLM Confidence (Dynamic)
```
confidence = base_confidence + success_bonus - failure_penalty + speed_bonus
```
Dynamic calculation based on:
- **Base Confidence**: 0.8 (configurable)
- **Success Bonus**: +0.1 if SQL executes successfully
- **Failure Penalty**: -0.3 if SQL execution fails
- **Speed Bonus**: +0.1 if response time < 2 seconds

## Supported Query Patterns

1. **Active loan count**: "How many active loans do we have?"
2. **Total disbursement**: "What is the total disbursement amount?"
3. **High risk loans**: "Show me loans with high risk"
4. **DPD analysis**: "Find loans with DPD greater than 90 days"
5. **Bounce rate**: "What is the bounce rate by product line?"
6. **Branch analysis**: "Show disbursements by branch"
7. **CIBIL analysis**: "What is the average CIBIL score by state?"
8. **High-value disbursements**: "Show recent disbursements above 10 lakhs"
9. **Geographic analysis**: "How many loans are there in Mumbai?"
10. **Low CIBIL loans**: "Show me loans with low CIBIL scores"

## Logging

All operations are logged to timestamped files in the `logs/` directory:
- **File Format**: `nl2sql_pipeline_YYYYMMDD_HHMMSS.log`
- **Log Levels**: INFO, WARNING, ERROR, DEBUG
- **Content**: Query processing, confidence calculations, execution times, errors

### Sample Log Entry
```
2025-05-30 09:45:23,123 - nl2sql_pipeline - INFO - Processing query: 'How many active loans do we have?' (force_offline=False)
2025-05-30 09:45:23,145 - nl2sql_pipeline - INFO - Pattern matched: active_loan_count (confidence: 0.756)
2025-05-30 09:45:23,167 - nl2sql_pipeline - INFO - SQL execution successful in 0.022s
2025-05-30 09:45:23,168 - nl2sql_pipeline - INFO - Pattern matching successful in 0.045s
```

## Data Files

### Pattern Statistics (`data/pattern_stats.json`)
Automatically maintained statistics for each pattern:
```json
{
  "active_loan_count": {
    "total_matches": 15,
    "successful_executions": 14,
    "failed_executions": 1,
    "avg_execution_time": 0.123,
    "last_used": "1717056789"
  }
}
```

### Pattern Configuration (`config/patterns.json`)
Auto-generated from defaults, can be customized:
```json
[
  {
    "id": "active_loan_count",
    "pattern": "how\\s+many.*active.*loan",
    "sql": "SELECT COUNT(*) as active_loans FROM {schema}.DM_RISK_MASTER WHERE ACTIVE_LAN = 1",
    "description": "Count of active loans",
    "keywords": ["count", "active", "loan"],
    "complexity": 1,
    "category": "aggregation"
  }
]
```

## Performance Metrics

- **Query Generation**: < 3 seconds (LLM), < 0.1 seconds (patterns)
- **Confidence Accuracy**: Statistical calculation based on historical data
- **Pattern Success Rate**: Tracked per pattern with automatic updates
- **Execution Time**: Logged for performance monitoring

## Error Handling

### Graceful Degradation
1. **LLM Unavailable**: Falls back to pattern matching
2. **Snowflake Unavailable**: Uses mock results for testing
3. **Pattern Not Found**: Returns helpful error message
4. **Configuration Error**: Uses sensible defaults

### Error Logging
All errors are logged with:
- Timestamp and severity level
- Detailed error description
- Context information (query, method attempted)
- Suggested resolution steps

## Security Features

- **BIU Utilities Integration**: Leverages existing security framework
- **Read-Only Access**: Uses `PROD_BIU_READ_ONLY_NS` role
- **Query Limits**: Automatic LIMIT clauses for safety
- **Input Validation**: Prevents dangerous SQL operations
- **Audit Trail**: Complete logging for compliance

## Development

### Adding New Patterns
1. Edit `config/patterns.json`
2. Add pattern with regex, SQL template, and metadata
3. Restart application to load new patterns
4. Statistics will be automatically tracked

### Customizing Confidence Calculation
1. Edit `config/pipeline_config.json`
2. Adjust weights in the `llm` and `patterns` sections
3. Changes take effect immediately

### Extending Functionality
- Add new methods to `ProfessionalNL2SQLPipeline` class
- Implement additional confidence calculation strategies
- Add new pattern categories and complexity factors

## Troubleshooting

### Common Issues

1. **Import Errors**
   - Ensure all files are in correct directories
   - Check Python path includes project root
   - Verify `biuutilities.py` is in the root directory

2. **Configuration Errors**
   - Verify `config/pipeline_config.json` is valid JSON
   - Check environment variables are set correctly

3. **Pattern Matching Fails**
   - Check pattern regex syntax in `config/patterns.json`
   - Verify schema placeholders are correct
   - Test with known working queries from `sample_queries.txt`

4. **Logging Issues**
   - Ensure `logs/` directory is writable
   - Check disk space availability
   - Check file permissions

### Debug Mode
```cmd
set LOG_LEVEL=DEBUG
python main.py --query "your query"
```

### If You Get Specific Errors
- **SSL Certificate Issues**: Use offline mode or check corporate network settings
- **Snowflake Connection**: Verify BIU utilities configuration
- **LLM API Issues**: System automatically falls back to patterns

## Production Deployment

### Recommended Steps
1. **Test Configuration**: Run `python main.py --status`
2. **Validate Patterns**: Test with sample queries
3. **Monitor Logs**: Set up log monitoring
4. **Performance Baseline**: Establish performance metrics
5. **Backup Configuration**: Save working configuration

### Monitoring
- Monitor log files for errors and performance
- Track pattern success rates over time
- Review confidence score distributions
- Monitor query execution times

## Key Improvements Made

### 1. Removed Hardcoded Values
- Statistical confidence calculation
- Configuration-driven parameters
- Data-driven pattern scoring

### 2. Professional Logging
- Timestamped log files in `logs/` directory
- No emojis in logs
- Centralized logging across all components
- Log levels: INFO, WARNING, ERROR, DEBUG

### 3. Configuration Management
- JSON configuration file: `config/pipeline_config.json`
- Environment variable overrides
- No hardcoded database settings

### 4. Robust Architecture
- Modular design with clear separation of concerns
- Statistical pattern matching
- Professional error handling
- Enterprise-grade security

## Support

For issues or questions:
1. Check log files in `logs/` directory
2. Run system diagnostics: `python main.py --status`
3. Review configuration in `config/pipeline_config.json`
4. Test with known working patterns from `sample_queries.txt`

## 🚀 Ready for Production

The system is now:
- ✅ **Professional**: No hardcoded values, proper logging
- ✅ **Robust**: Statistical confidence, error handling
- ✅ **Configurable**: JSON configuration, environment variables
- ✅ **Maintainable**: Clean architecture, modular design
- ✅ **Enterprise-ready**: Security, governance, audit trails

**Start with**: `python main.py --status` to verify everything is working!

---

**Built for enterprise reliability with professional logging, statistical confidence calculation, and robust error handling.**
